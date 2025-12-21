In the deferred lighting pas, we iterate over each of the G-buffer textures pixel by pixel and use their content as input to the lighting algorithms. Because the G-buffer texture values all represent the final transformed fragment values, we only have to do the expensive lighting operations once per pixel. This is useful in complex scenes where we'd easily invoke multiple expensive fragment shader calls per pixel in a forward rendering setting.

### Lighting Pass

We bind all relevant textures of the G-buffer before rendering and also send the lighting uniform variables to the shader

```c++
void lightingPass(Scene scene) {
  Shader screen = scene.shaders.screen;
  glBindFramebuffer(GL_FRAMEBUFFER, 0);
  glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
  screen.use();
  screen.setInt("positionBuffer", 0);
  screen.setInt("normalBuffer", 1);
  screen.setInt("albedoBuffer", 2);
  glActiveTexture(GL_TEXTURE0);
  glBindTexture(GL_TEXTURE_2D, scene.buffers.gPosition);
  glActiveTexture(GL_TEXTURE1);
  glBindTexture(GL_TEXTURE_2D, scene.buffers.gNormal);
  glActiveTexture(GL_TEXTURE2);
  glBindTexture(GL_TEXTURE_2D, scene.buffers.gColor);

  for (int i=0; i<scene.lights.size(); ++i) {
    screen.setVec3("lights[" + to_string(i) + "].position", scene.lights[i].position);
    screen.setVec3("lights[" + to_string(i) + "].colour", scene.lights[i].colour);
  }
  renderQuad(scene.vertices.quad);
}
```
### Fragment Shader

The fragment shader is similar to previous lighting shaders, but we retrieve the values from texture samples from the G-buffer.

```
#version 330 core

in vec2 texCoords;

uniform sampler2D positionBuffer;
uniform sampler2D normalBuffer;
uniform sampler2D albedoBuffer;

out vec4 FragColor;

struct Light {
  vec3 position;
  vec3 colour;
};

const int NUM_LIGHTS = 32;
uniform Light lights[NUM_LIGHTS];

void main() {
  vec3 position = texture(positionBuffer, texCoords).rgb;
  vec3 normal = texture(normalBuffer, texCoords).rgb;
  vec3 colour = texture(albedoBuffer, texCoords).rgb;
  float specular = texture(albedoBuffer, texCoords).a;

  vec3 ambient = 0.1 * colour;
  vec3 diffuse = vec3(0.0);

  for (int i=0; i<NUM_LIGHTS; ++i) {
    vec3 lightDir = normalize(lights[i].position - position);
    float diff = max(dot(lightDir, normal), 0.0);
    vec3 result = lights[i].colour * diff * colour;
    float distance = length(lights[i].position - position);
    result *= 1.0 / (distance * distance);
    diffuse += result;
  }
  vec3 lighting = ambient + diffuse;

  FragColor = vec4(lighting, 1.0);
}
```

One of the disadvantages of deferred shading is that it is not possible to do blending as all values in the G-buffer are from single fragments, and blending operates on the combinations of multiple fragments. Another disadvantage is that deferred shading forces you to use the same lighting algorithm for most of your scene's lighting.

To overcome these disadvantages, we often split the renderer into two parts: one deferred rendering part and the other a forward rendering part specifically meant for blending or special shader effects not suited for a deferred rendering pipeline.