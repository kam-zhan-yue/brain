The first pass requires us to create a shadow/depth map. This is the depth texture as rendered from the light's perspective that we use for testing.

We use a framebuffer object to render the depth map.

```c++
unsigned int depthMapFBO;
glGenFramebuffers(1, &depthMapFBO);
```

Then we create a 2D texture for the framebuffer's depth buffer. Because we only care about depth values, we specify the texture's formats as `GL_DEPTH_COMPONENT`. We also use a width and height of 1024, this is the resolution of the depth map.

```c++
const unsigned int SHADOW_WIDTH = 1024, SHADOW_HEIGHT = 1024;

unsigned int depthMap;
glGenTextures(1, &depthMap);
glBindTexture(GL_TEXTURE_2D, depthMap);
glTexImage2D(GL_TEXTURE_2D, 0, GL_DEPTH_COMPONENT, SHADOW_WIDTH, SHADOW_HEIGHT, 0, GL_DEPTH_COMPONENT, GL_FLOAT, NULL);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);
```

We can then attach the generated depth texture to the framebuffer's depth buffer.

```c++
glBindFramebuffer(GL_FRAMEBUFFER, depthMapFBO);
glFramebufferTexture2D(GL_FRAMEBUFFER, GL_DEPTH_ATTACHMENT, GL_TEXTURE_2D, depthMap, 0);
glDrawBuffer(GL_NONE);
glReadBuffer(GL_NONE);
glBindFramebuffer(GL_FRAMEBUFFER, 0);
```

We only need the depth information when rendering the scene from the light's perspective, so there is no need for a colour buffer. A framebuffer object isn't complete without a colour buffer, so we tell OpenGL we don't render any colour data by setting the read and draw buffer to GL_NONE.

Finally, we generate the depth map.

```c++
// First Pass
glViewport(0, 0, SHADOW_WIDTH, SHADOW_HEIGHT);
glBindFramebuffer(GL_FRAMEBUFFER, depthMapFBO);
glClear(GL_DEPTH_BUFFER_BIT);
setupShaders();
renderScene();

// Second Pass
glViewport(0, 0, SCREEN_WIDTH, SCREEN_HEIGHT);
glBindFramebuffer(GL_FRAMEBUFFER, 0);
glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
glBindTexture(GL_TEXTURE_2D, depthMap);
setupShaders();
renderScene();
```

Because shadow maps often have a different resolution compared to what we originally render the scene in, we need to change the viewport parameters to accommodate for the size of the shadow map.

### Light Space Transform
In the first pass, we need to use a different projection and view matrix to render the scene from the light's point of view. For a directional light source, all light rays are parallel. Hence, we use an orthographic projection matrix for the light source where there is no perspective to deform.

```c++
float nearPlane = 1.0f, farPlane = 7.5f;
glm::mat4 lightProjection = glm::ortho(-10.0f, 10.0f, -10.0f, 10.0f, nearPlane, farPlane);
```

Because a projection matrix indirectly determines the range of what is visible, you want to make sure the size of the projection frustum correctly contains the objects you want to be in the depth map. Objects or fragments not included in the depth map will not produce shadows.

To create a view matrix for the light source, we can use the `glm::lookAt` function with the light source's position looking at the screen's centre.

```c++
glm::mat4 lightView = glm::lookAt(glm::vec3(....),
								glm::vec3(0.0, 0.0, 0.0),
								glm::vec3(0.0, 1.0, 0.0),
								)
```

Combining these two gives us a light space transformation that transforms each world space vector into the space as visible from the light source. To save performance, we can use a simple shader for rendering to the depth map.

## Rendering to the Depth Map

When we render the scene from the light's perspective, we want to use a simple shader to transform the vertices to light space.

```glsl
#verison 330 core
layout (location = 0) in vec3 aPos;

uniform mat4 lightSpaceMatrix;
uniform mat4 model

void main() {
	gl_Position = lightSpaceMatrix * model * vec4(aPos, 1,0)l
}
```

The fragment shader can be empty since we have no colour buffer. The empty fragment shader does no processing, but it will update the depth buffer at the end of its run.

Rendering the depth/shadow map is then:

```c++
simpleDepthShader.use();
simpleDepthShader.setMat4("lightSpaceMatrix", lightMatrix);
glViewport...
```

