The SSAO shader runs on a 2D screen-filled quad that calculates the occlusion value for each of its fragments. As we need to store the result of the SSAO stage (for use in the final lighting shader), we need to create yet another framebuffer object.

```c++
// SSAO Framebuffer
unsigned int ssaoFBO;
glGenFramebuffers(1, &ssaoFBO);
glBindFramebuffer(GL_FRAMEBUFFER, ssaoFBO);
unsigned int ssaoColourBuffer;
glGenTextures(1, &ssaoColourBuffer);
glBindTexture(GL_TEXTURE_2D, ssaoColourBuffer);
glTexImage2D(GL_TEXTURE_2D, 0, GL_RED, windowWidth, windowHeight, 0, GL_RED, GL_FLOAT, NULL);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_2D, ssaoColourBuffer, 0);
```

As the ambient occlusion result is a single grayscale value, we'll only need a texture's red component, hence setting the internal format to `GL_RED`.

The SSAO rendering pass takes in the relevant G-buffer textures, the noise texture, and the normal-oriented hemisphere kernel samples.

```c++
void ssaoPass(Scene scene) {
  Shader ssao = scene.shaders.ssao;
  glBindFramebuffer(GL_FRAMEBUFFER, scene.buffers.ssao);
  glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
  ssao.use();
  ssao.setInt("positionBuffer", 0);
  ssao.setInt("normalBuffer", 1);
  ssao.setInt("noiseBuffer", 2);

  glActiveTexture(GL_TEXTURE0);
  glBindTexture(GL_TEXTURE_2D, scene.buffers.gPosition);
  glActiveTexture(GL_TEXTURE1);
  glBindTexture(GL_TEXTURE_2D, scene.buffers.gNormal);
  glActiveTexture(GL_TEXTURE2);
  glBindTexture(GL_TEXTURE_2D, scene.ssao.noiseTexture);
  renderQuad(scene.vertices.quad);
}
```

## Fragment Shader

```
#version 330 core

in vec2 texCoords;

uniform sampler2D positionBuffer;
uniform sampler2D normalBuffer;
uniform sampler2D noiseBuffer;

out vec4 FragColor;

uniform vec3 samples[64];
uniform mat4 projection;

// tile noise texture over screen, based on screen dimensions / noise size
const vec2 noiseScale = vec2(800.0/4,0, 600.0/4.0);
```

The `noiseScale` variable exists because `texCoords` vary between 0.0 and 1.0 and the noiseBuffer won't tile at all. So, we need to calculate the required amount to scale `texCoords` by diving the screen's dimensions by the noise texture size.

```
void main() {
  vec3 fragPos = texture(positionBuffer, texCoords).xyz;
  vec3 normal = texture(normalBuffer, texCoords).xyz;
  vec3 randomVec = texture(noiseBuffer, texCoords * noiseScale).xyz;
}
```

As we set the tiling parameters to GL_REPEAT, the random values will be repeated all over the screen. Together with the position and normal vector, we have enough data to create a TBN matrix that transforms any vector from tangent-space to view-space:

```
vec3 tangent = normalize(randomVec - normal * dot(randomVec, normal));
vec3 bitangent = cross(normal, tangent);
mat3 TBN = mat3(tangent, bitangent, normal);
```

### Kernel
Next, we need to iterate over each of the kernel samples, transform the samples from tangent to view-space, add them to the current fragment position, and compare the fragment position's depth with the sample depth stored in the view-space position buffer.

```
  float occlusion = 0.0;
  for (int i=0; i<KERNEL_SIZE; ++i) {
    vec3 kernelSample = TBN * [i]; // from tangent to view-space
    kernelSample = fragPos + kernelSample * RADIUS;
  }
```

- Here, KERNEL_SIZE and RADIUS are variables that we can use to tweak the effect.
- For each iteration, we transform the respective sample to view-space
- Then, we multiple the offset sample by radius to change the effective sample radius of SSAO
- Then, we add the view-space kernel offset sample to the view-space fragment position

Next, we want to transform `sample` to screen-space so we can sample the position/depth value of `sample` as if we were rendering its position directly to the screen. As the vector is currently in view-space, we transform it to clip-space using the `projection` matrix uniform;

```
vec4 offset = vec4(kernelSample, 1.0);
offset = projection * offset;         // from view to clip-space
offset.xyz /= offset.w;               // perspective divide
offset.xyz = offset.xyz * 0.5 + 0.5;  // transform to range 0.0 to 1.0
```

- Transform the variable from view-space to clip-space
- Perform perspective divide to normalise to NDC
- Transform to 0.0 - 1.0 to sample the position texture

```
// 3. Get the depth of the randomly picked fragment
float sampleDepth = texture(positionBuffer, offset.xy).z;
occlusion += (sampleDepth >= kernelSample.z + BIAS ? 1.0 : 0.0);
```

- Use the offset's x and y components to sample the position texture and retrieve the depth of the sample position as seen from the viewer's perspective (the first non-occluded visible fragment).
- Check if the sample's current depth value is larger than the stored depth, and add to the final contribution factor if so
- Add a small bias to the original fragment's depth to visually tweak the SSAO effect and solve acne effects that may occur based on the scene's complexity.

Finally, we need to take into account a range check. Whenever a fragment is tested for ambient occlusion that is aligned close to the edge of a surface, it will also consider depth values of surfaces far behind the test surface. These values will (incorrectly) contribute to the occlusion factor.

The range check will make sure a fragment contributes to the occlusion factor if its depth values is within the sample's radius.

```
float rangeCheck = smoothstep(0.0, 1.0, radius / abs(fragPos.z - sampleDepth));
occlusion += (sampleDepth >= kernelSample.z + BIAS ? 1.0 : 0.0) * rangeCheck;
```

- The `smoothstep` function will smoothly interpolate its third parameter between the first and second parameter's range, returning 0.0 if less than or equal to its first parameter and 1.0 if equal to its second parameter.
- If the depth difference ends up between `radius`, its value gets smoothly interpolated between 0.0 and 1.0
- If we were to use a hard cut-off range, it would abruptly remove occlusion contributions if the depth values are outside `radius`.

Finally, we need to normalise the occlusion contribution by the size of the kernel and output the results. We subtract the occlusion factor from 1.0 so that we can use the occlusion factor to scale the ambient lighting component.

