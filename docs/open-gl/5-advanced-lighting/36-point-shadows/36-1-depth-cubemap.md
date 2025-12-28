Shadow mapping works great, but it is mostly suited for directional (or spot) lights as the shadows are generated only in the direction of the light source. It is therefore also known as directional shadow mapping as the depth (or shadow) map is generated from only the direction the light is looking at.

Omnidirectional shadow maps is the generation of dynamic shadows in all surrounding directions. This technique is perfect for point lights as a real point light would cast shadows in all directions.

The technique is mostly similar to directional shadow mapping: we generate a depth map from the light's perspective(s), sample the depth map based on the current fragment position, and compare each fragment with the stored depth value to see whether it is in shadow. The main difference between directional shadow mapping and omnidirectional shadow mapping is the depth map we use.

The depth map we need requires rendering a scene from all surrounding directions of a point light and as such a normal 2D depth map won't work. However, a cubemap can store full environment data with only 6 faces, so it is possible to render the render scene to each of the faces of a cubemap and sample these as the point light's surrounding depth values.

The generated depth cubemap is then passed to the lighting fragment shader that samples the cubemap with a direction vector to obtain the closest depth at that fragment.

## Cubemap Generation
To create a cubemap of a light's surrounding depth values, we have to render the scene 6 times: once for each face. One obvious way to do this is to render the scene 6 times with 6 different view matrices, each time attaching a different cubemap face to the framebuffer object.

```c++
for (unsigned int i = 0; i < 6; ++i) {
	GLenum face = GL_TEXTURE_CUBE_MAP_POSITIVE_X + i;
	glFramebufferTexture2D(GL_FRAMEBUFFER, GL_DEPTH_ATTACHMENT, face, depthCubemap, 0);
	BindViewMatrix(lightViewMatrices[i]);
	RenderScene();
}
```

This can be quite expensive though as a lot of render calls are necessary for this single depth map. We can use a more organised approach using a little trick in the geometry shader that allows us to build the depth cubemap with a single render pass.

```c++
  // ============DEPTH CUBEMAP==============//
  
  // Generate a depth cubemap
  unsigned int depthCubemap;
  glGenTextures(1, &depthCubemap);

  // Assign each of the single cubemap faces a 2D depth-valued texture image
  glBindTexture(GL_TEXTURE_CUBE_MAP, depthCubemap);
  for (unsigned int i = 0; i < 6; ++i) {
    glTexImage2D(GL_TEXTURE_CUBE_MAP_POSITIVE_X + i, 0, GL_DEPTH_COMPONENT, SHADOW_WIDTH, SHADOW_HEIGHT, 0, GL_DEPTH_COMPONENT, GL_FLOAT, NULL);
  }

  // Set texture parameters
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE);
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_R, GL_CLAMP_TO_EDGE);

  // Generate the framebuffer object for the depth cubemap
  unsigned int depthMapFBO;
  glGenFramebuffers(1, &depthMapFBO);
  glBindFramebuffer(GL_FRAMEBUFFER, depthMapFBO);
  glFramebufferTexture(GL_FRAMEBUFFER, GL_DEPTH_ATTACHMENT, depthCubemap, 0);
  glDrawBuffer(GL_NONE);
  glReadBuffer(GL_NONE);
  glBindFramebuffer(GL_FRAMEBUFFER, 0);
```

## Light Space Transform

With the framebuffer and the cubemap set, we need some way to transform all the scene's geometry to the relevant light spaces in all 6 directions of the light. We will need a light space transformation matrix T, but this time one for each face.

Each light space transformation matrix contains both a projection and a view matrix. For the projection matrix, we're going to use a perspective projection matrix; the light source represents a point in space so perspective projection makes most sense. Each light space transformation matrix uses the same projection matrix.

```c++
float aspect = (float)SHADOW_WIDTH / (float)SHADOW_HEIGHT;
float near = 1.0f;
float far = 25.0f;
glm::mat4 shadowProj = glm::perspective(glm::radians(90.0f), aspect, near, far);
```

We set the FOV parameter to 90 degrees to make sure the viewing field is exactly large enough to fill a single face of the cubemap such that all faces align correctly to each other at the edges.

As the projection matrix does not change per direction, we can re-use it for each of the 6 transformation matrices. We need a different matrix per direction. We can create 6 view directions using `glm::lookAt`.

```c++
std::vector<glm::mat4> shadowTransforms;
shadowTransforms.push_back(shadowProj * glm::lookAt(lightPos, lightPos + glm::vec3(1.0, 0.0, 0.0), glm::vec3(0.0, -1.0, 0.0)));
shadowTransforms.push_back(shadowProj * glm::lookAt(lightPos, lightPos + glm::vec3(1.0, 0.0, 0.0), glm::vec3(0.0, -1.0, 0.0)));
// too lazy to finish
```

We create 6 view matrices and multiply them with the projection matrix to get a total of 6 different light space transformation matrices. The `target` parameter of `glm::lookAt` each looks into the direction of a single cubemap face.

These transformation matrices are sent to the shaders that render the depth into the cubemap.

## Depth Shaders

To render depth values to a depth cubemap, we're going to need a total of three shaders: a vertex shader, a geometry shader, and a fragment shader.

The geometry shader will be the shader responsible for transforming all world-space vertices to the 6 different light spaces. Therefore, the vertex shader simply transforms vertices to world-space and directs them to the geometry shader.

**Vertex Shader**
```glsl
#version 330 core

layout (location = 0) in vec3 aPos;

uniform mat4 model;

void main() {
  gl_Position = model * vec4(aPos, 1.0);
}
```

**Geometry Shader**
```glsl
#version 330 core

layout (triangles) in;
layout (triangle_strip, max_vertices=18) out;

uniform mat4 shadowMatrices[6];

out vec4 FragPos;

void main() {
  for (int face = 0; face < 6; ++face) {
    gl_Layer = face; // built-in variable to which face we render
    for (int i=0; i<3; ++i) { // for each triangle vertex
      FragPos = gl_in[i].gl_Position;
      gl_Position = shadowMatrices[face] * FragPos;
      EmitVertex();
    }
    EndPrimitive();
  }
}
```

The geometry shader has a built-in variable called `gl_Layer` that specifies which cubemap face to emit a primitive to. When left alone, the geometry shader just sends its primitives further down the pipeline as usual, but when we update this variable, we can control to which cubemap face we render to for each primitive. This only works when we have a cubemap texture attached to the active framebuffer.

**Fragment Shader**
```glsl
#version 330 core

layout (location = 0) in vec3 aPos;

uniform mat4 model;

void main() {
  gl_Position = model * vec4(aPos, 1.0);
}
```

We calculate our own linear depth as the linear distance between each closest fragment position and the light source's position. Calculating our own depth values makes the later shadow calculations a bit more intuitive.