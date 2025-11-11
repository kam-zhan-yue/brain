---
id: 29-5-uniform-buffer-objects
aliases: []
tags: []
---

When using more than one shader, we have to continuously set uniform variables when most of them are the same for each shader. Uniform buffer objects allow us to declare a set of global uniform variables that remain the same over any number of shader programs.

Uniform buffer objects set the relevant uniforms only once in fixed GPU memory. It is a buffer like any other, so we need to use `glGenBuffer`, bind it to the `GL_UNIFORM_BUFFER`, and store all relevant uniform data into the buffer.

```glsl
#version 330 core

layout (location = 0) in vec3 aPos;
layout (std140) uniform Matrices {
    mat4 projection;
    mat4 view;
};

uniform mat4 model;
```

We declared a uniform block `Matrices` to store two 4x4 matrices. Variables in a uniform block can be directly accessed without the block name as a prefix. (projection instead of Matrices.projection). We store these matrix values in a buffer somewhere in OpenGL code and each shader that declares this uniform block as access to the data.

`layout (std14)` means that the currently defined uniform block uses a specific memory layout for its content. This statement sets the uniform block layout.
