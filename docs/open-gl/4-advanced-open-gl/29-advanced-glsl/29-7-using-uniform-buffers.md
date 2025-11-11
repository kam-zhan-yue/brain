---
id: 29-7-using-uniform-buffers
aliases: []
tags: []
---

To create a uniform buffer object (UBO), we need to generate, bind, then populate, then unbind.

```c++
unsigned int UBO;
glGenBuffers(1, &UBO);
glBindBuffer(GL_UNIFORM_BUFFER, UBO);
glBufferData(GL_UNIFORM_BUFFER, 152, NULL, GL_STATIC_DRAW); // 152 bytes
glBindBuffer(GL_UNIFORM_BUFFER, 0);
```

Whenever we want to update or insert data, we bind to `UBO` and use `glBufferSubData` to update its memory. We only have to update this uniform buffer and all shaders that use this buffer now use its updated data.

## Binding Points

In order for OpenGL to know what uniform buffers correspond to which uniform block, it uses a context called binding points. Once we create a uniform buffer, we link it to a binding point and also link the uniform block in the shader to the same binding point, effectively linking them together.

![[29-7-binding-points.png]]

We can bind multiple uniform buffers to different binding points. Because shader A and shader B both have a uniform block linked to the same binding point 0, their uniform blocks share the same uniform data found in `uboMatrices`; a requirement being that both shaders defined the same `Matrices` uniform block.

To set a shader uniform block to a speicific binding point, we call `glUniformBlockBinding`. This takes a program object, a uniform block index, and the binding point to link to. The uniform block index is a location index of the defined uniform block in the shader. This can be retrieved via a call to `glGetUniformBlockIndex` that accepts a program object and the name of the uniform block.

We can set the `Lights` uniform block to binding point 2:

```c++
unsigned int lightsIndex = glGetUniformBlockIndex(shaderA.ID, "Lights");
glUniformBlockBinding(shaderA.ID, lightsIndex, 2);
```

This process needs to be repeat for each shader.

> In version 4.2, we can store the binding point of a uniform block in the shader with a layout specifier.

```glsl
layout (std140, binding = 2) uniform Lights { ... };
```

Then, we need to bind the uniform buffer object to the same binding point.

```c++
glBindBufferBase(GL_UNIFORM_BUFFER, 2, uboExampleBlock);
// or
glBindBufferRange(GL_UNIFORM_BUFFER, 2, uboExampleBlock, 0, 152);
```

- `glBindBufferBase` links the block to a binding point
- `glBindBufferRange` uses an extra offset and size parameter to bind only a specific range of the uniform buffer to a binding point. You could have multiple different uniform blocks linked to a single uniform buffer object.

Finally, we can add data to the uniform buffer. We can add it as a sngle array or update parts of the buffer whenever we want.

```c++
glBindBuffer(GL_UNIFORM_BUFFER, UBO);
int b = true; // bools in GLSL are defined as 4 bytes, so store as integer
glBufferSubData(GL_UNIFORM_BUFFER, 144, 4, &b);
glBindBuffer(GL_UNIFORM_BUFFER, 0);
```

## Conclusion
UBOs have several advantage over single uniforms.
- Setting a lot of uniforms at once is faster than setting multiple uniforms one at a time.
- If you want to change the same uniform over several shaders, it is much easier to change a uniform once in a uniform buffer
- You can use a lot more uniforms in shaders using uniform buffer objects. OpenGL has a limit to how much uniform data it can handle which can be queried with `GL_MAX_VERTEX_UNIFORM_COMPONENTS`. When using UBOs, this limit is much higher.
