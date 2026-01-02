Taking the high dynamic range of your scene's lighting into account in a PBR pipeline is important. As PBR bases most of its inputs on real physical properties and measurements, it makes sense to closely match the incoming light values to their physical equivalents. Whether we make educated guesses on each light's radiant flux or use their direct physical equivalent, the difference between a simple light bulb or the Sun is significant either way. Without working in a HDR render environment, it's impossible to correctly specify each light's relative intensity.

So, PBR and HDR go hand-in-hand. For IBL, we base the environment's indirect light intensity on the colour values of an environment cubemap. Hence, we need a way to store the lighting's high dynamic range into an environment map.

The environment map we've been using is a cubemap in low dynamic range (LDR).

## Radiance HDR File Format

To solve this, we use a radiance file format (`.hdr` extension), which stores a cubemap as floating point data. This allows us to specify colour values outside the 0.0 to 1.0 range to give lights their correct colour intensities.

The file format also uses a clever trick to store each floating point value, not as a 32 bit value per channel, but 8 bits per channel using the colour's alpha channel as an exponent (this comes with a loss of precision). This works well, but requires the parsing program to re-convert each colour to their floating point equivalent.

Examples of HDR environment maps look like:

![[45-1-radiance-file.png]]

The image appears distorted and doesn't show all 6 individual cubemap faces. The environment map is projected from a sphere onto a flat plane so that we can more easily store the environment into a single image known as an equirectangular map.

This comes with a caveat as most of the visual resolution is stored in the horizontal view direction, while less is preserved in the bottom and top directions. In most cases, this is a decent compromise as with almost any renderer, the interesting lighting and surrounding happens in the horizontal viewing directions.

### HDR and `stb_image.h`

Loading radiance HDR images requires knowledge of the file format, which isn't too difficult. The `stb_image` library includes this with:

```c++
stbi_set_flip_vertically_on_load(true);
int width, height, nrComponents;
string resourcePath = (std::string(RESOURCES_DIR) + "/textures/hdr/newport_loft.hdr");
float *data = stbi_loadf(resourcePath.c_str(), &width, &height, &nrComponents, 0);

unsigned int hdrTexture;
if (data) {
	glGenTextures(1, &hdrTexture);
	glBindTexture(GL_TEXTURE_2D, hdrTexture);
	glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB16F, width, height, 0, GL_RGB, GL_FLOAT, data);
	glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE);
	glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);
	glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
	glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
	stbi_image_free(data);
} else {
	cout << "Failed to load HDR image at " << resourcePath << endl;
}

```

`stb_image.h` automatically maps the HDR values to a list of floating point values: 32 bits per channel and 3 channels per colour by default.

### From Equirectangular to Cubemap

It is possible to use the equirectangular map directly for environment lookups, but these operations can be relatively expensive. Hence, we will convert the equirectangular map to a cubemap.

To convert, we need to render a unit cube and project the equirectangular map on all of the cube's faces from the inside and take 6 images of each of the cube's sides as a cubemap face. 

The vertex shader of this cube simply renders the cube as is and passes its local position to the fragment shader as a 3D sample vector.

```glsl
#version 330 core
layout (location = 0) in vec3 aPos;

out vec4 localPos;

uniform mat4 projection;
uniform mat4 view;

void main() {
  gl_Pos = projection * view * vec4(aPos, 1.0);
  localPos = aPos;
}
```


The fragmnet shader will colour each part of the cube as if we neatly folded the equirectangular map onto each side of the cube. To accomplish this, we take the fragment's sample direction as interpolated from the cube's local position and then use this direction vector and some trigonometry magic (spherical to cartesian) to sample the equirectangular map as if it is a cubemap itself. We directly store the result onto the cube-face's fragment.

```glsl
#version 330 core

in vec3 localPos;
out vec4 FragColor;
uniform sampler2D equirectangularMap;

const vec2 invAtan = vec2(0.1581, 0.3183);

vec2 sampleSphericalMap(vec3 v) {
  vec2 uv = vec2(atan(v.z, v.x), asin(v.y));
  uv *= invAtan;
  uv += 0.5;
  return uv;
}

void main() {
  vec2 uv = sampleSphericalMap(normalize(localPos));
  vec3 color = texture(equirectangularMap, uv).rgb;
  FragColor = vec4(color, 1.0);
}
```

This allows us to map an equirectangular image to a cubic shape, but we need to convert the source HDR image to a cubemap texture. To accomplish this, we need to render the same cube 6 times, looking at each individual face of the cube, while rendering its visual result with a framebuffer object.

```c++
Environment generateEnvironment() {
  // Shader
  Shader cubemapShader = Shader(
    (string(SHADER_DIR) + "/cubemap-vertex.glsl").c_str(),
    (string(SHADER_DIR) + "/cubemap-fragment.glsl").c_str()
  );

  // Texture
  stbi_set_flip_vertically_on_load(true);
  int width, height, nrComponents;
  string resourcePath = (std::string(RESOURCES_DIR) + "/textures/hdr/newport_loft.hdr");
  float *data = stbi_loadf(resourcePath.c_str(), &width, &height, &nrComponents, 0);
  unsigned int hdrTexture;
  if (data) {
    glGenTextures(1, &hdrTexture);
    glBindTexture(GL_TEXTURE_2D, hdrTexture);
    glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB16F, width, height, 0, GL_RGB, GL_FLOAT, data);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
    stbi_image_free(data);
  } else {
    cout << "Failed to load HDR image at " << resourcePath << endl;
  }

  // Framebuffer
  unsigned int cubemapFBO, cubemapRBO;
  glGenFramebuffers(1, &cubemapFBO);
  glGenRenderbuffers(1, &cubemapRBO);
  glBindFramebuffer(GL_FRAMEBUFFER, cubemapFBO);
  glBindRenderbuffer(GL_RENDERBUFFER, cubemapRBO);
  glRenderbufferStorage(GL_RENDERBUFFER, GL_DEPTH_COMPONENT24, 512, 512);
  glFramebufferRenderbuffer(GL_FRAMEBUFFER, GL_DEPTH_ATTACHMENT, GL_RENDERBUFFER, cubemapRBO);
  unsigned int environmentCubemap;
  glGenTextures(1, &environmentCubemap);
  glBindTexture(GL_TEXTURE_CUBE_MAP, environmentCubemap);
  // store each face with 16 bit floating point values
  for (unsigned int i = 0; i < 6; ++i) {
    glTexImage2D(GL_TEXTURE_CUBE_MAP_POSITIVE_X + i, 0, GL_RGB16F, 512, 512, 0, GL_RGB, GL_FLOAT, nullptr);
  }
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE);
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_R, GL_CLAMP_TO_EDGE);
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_MAG_FILTER, GL_LINEAR);

  // Capture to each side of the cubemap
  mat4 captureProjection = perspective(radians(90.0f), 1.0f, 0.1f, 10.0f);
  mat4 captureViews[] =
  {
    lookAt(vec3(0.0f, 0.0f, 0.0f), vec3( 1.0f,  0.0f,  0.0f), vec3(0.0f, -1.0f,  0.0f)),
    lookAt(vec3(0.0f, 0.0f, 0.0f), vec3(-1.0f,  0.0f,  0.0f), vec3(0.0f, -1.0f,  0.0f)),
    lookAt(vec3(0.0f, 0.0f, 0.0f), vec3( 0.0f,  1.0f,  0.0f), vec3(0.0f,  0.0f,  1.0f)),
    lookAt(vec3(0.0f, 0.0f, 0.0f), vec3( 0.0f, -1.0f,  0.0f), vec3(0.0f,  0.0f, -1.0f)),
    lookAt(vec3(0.0f, 0.0f, 0.0f), vec3( 0.0f,  0.0f,  1.0f), vec3(0.0f, -1.0f,  0.0f)),
    lookAt(vec3(0.0f, 0.0f, 0.0f), vec3( 0.0f,  0.0f, -1.0f), vec3(0.0f, -1.0f,  0.0f))
  };
  cubemapShader.use();
  cubemapShader.setMat4("projection", captureProjection);
  cubemapShader.setInt("equirectangularMap", 0);
  glActiveTexture(GL_TEXTURE0);
  glBindTexture(GL_TEXTURE_2D, hdrTexture);
  Cube cube = Cube();
  glViewport(0, 0, 512, 512);
  glBindFramebuffer(GL_FRAMEBUFFER, cubemapFBO);
  for (unsigned int i = 0; i < 6; ++i) {
    cubemapShader.setMat4("view", captureViews[i]);
    glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_CUBE_MAP_POSITIVE_X + i, environmentCubemap, 0);
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
    cube.draw();
  }

  if (glCheckFramebufferStatus(GL_FRAMEBUFFER) != GL_FRAMEBUFFER_COMPLETE)
    cout << "Framebuffer is not complete." << endl;

  glBindFramebuffer(GL_FRAMEBUFFER, 0);
  glBindRenderbuffer(GL_RENDERBUFFER, 0);
  glBindTexture(GL_TEXTURE_CUBE_MAP, 0);

  return {
    .texture = environmentCubemap,
  };
}
```

Then, we can render the cubemap using a simple skybox shader.

Vertex Shader
```glsl
#version 330 core

layout (location = 0) in vec3 aPos;

uniform mat4 projection;
uniform mat4 view;

out vec3 localPos;

void main() {
  localPos = aPos;

  mat4 rotView = mat4(mat3(view)); // remove translation
  vec4 clipPos = projection * rotView * vec4(localPos, 1.0);

  gl_Position = clipPos.xyww;
}
```

The `xyww` trick ensures that the depth value of the rendered cube fragments always end up at 1.0, the maximum depth value. We also need to change the depth comparison function to `GL_LEQUAL`.

The fragment shader then directly samples the cubemap environment map using the cube's local fragment position.
```glsl
#version 330 core

in vec3 localPos;
out vec4 FragColor;
uniform sampler2D environmentCubemap;

void main() {
  vec3 colour = texture(environmentCubemap, localPos).rgb;
  colour = colour / (colour + vec3(1.0));
  colour = pow(colour, vec3(1.0 / 2.2));
  FragColor = vec4(colour, 1.0);
}
```

We sample the environment map using its interpolated vertex cube positions that directly correspond to the correct direction vector to sample. Seeing as the camera's translation components are ignored, rendering this shader over a cube should give you the environment map as a non-moving background. Also, as we output the environment map's HDR values to the default LDR framebuffer, we want to properly tone map the colour values. Furthermore, almost all HDR maps are in linear colour space by default so we need to apply gamma correction before writing to the default framebuffer.

Rendering now looks like:
```glsl
void renderEnvironment(Scene scene) {
  Shader shader = scene.shaders.background;
  shader.use();
  shader.setMat4("view", camera.getLookAt());
  shader.setMat4("projection", camera.getPerspective());
  shader.setInt("environmentCubemap", 0);
  glActiveTexture(GL_TEXTURE0);
  glBindTexture(GL_TEXTURE_CUBE_MAP, scene.environment.texture);
  scene.shapes.cube.draw();
  glBindTexture(GL_TEXTURE_CUBE_MAP, 0);
}
```