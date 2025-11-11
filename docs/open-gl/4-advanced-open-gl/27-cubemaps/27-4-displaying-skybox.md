We need another VAO and VBO to render the skybox as it is a cube.

A cubemap used to texture a 3D cube can be sampled using the local positions of the cube as its texture coordinates. When a cube is centred on the origin (0, 0, 0), each of its position vectors is also a direction vector from the origin. This direction vector is what we need to get the corresponding texture value at the cube's position. Hence, we only need to supply position vectors and don't need texture coordinates.

The vertex shader for the skybox is as follows:

```glsl
#version 330 core
layout (location = 0) in vec3 aPos;

out vec3 TexCoords;

uniform mat4 view;
uniform mat4 projection;

void main() {
	TexCoords = aPos;
	gl_Position = projection * view * vec4(aPos, 1.0);
}
```

The fragment shader is as follows:

```glsl
#version 330 core

in vec3 TexCoords;
out vec4 FragColor;

uniform samplerCube skybox;

void main() {
	FragColor = texture(skybox, TexCoords);
}
```

To render the skybox shader, we need to draw it as the first object in the scene and disable any depth testing.

```c++
glDepthMask(GL_FALSE);
skyboxShader.use();
// set view and projection matrices
glBindVertexArray(skyboxVAO);
glActiveTexture(GL_TEXTURE0);
glBindTexture(GL_TEXTURE_CUBE_MAP, skyboxTexture);
glDrawArrays(GL_TRIANGLES, 0, 36);
glDepthMask(GL_TRUE);
// draw rest of the scene
```

However, the current view matrix transforms all the skybox's positions by rotating, scaling, and transforming the. If the player moves, the cubemap moves as well. We want to remove the translation part of the view matrix. We can remove the translation section of transformation matrices by taking the upper-left 3x3 matrix of the 4x4 matrix. We can achieve this by converting the view matrix to a 3x3 matrix and converting it back to a 4z4 matrix.

```c++
glm::mat4 view = glm::mat4(glm::mat3(camera.getLookAt()));
```

This removes any translation, but keeps all rotation transformations so the user can still look around the scene.