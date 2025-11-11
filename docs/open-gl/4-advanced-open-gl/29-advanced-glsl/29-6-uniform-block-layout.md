---
id: 29-6-uniform-block-layout
aliases: []
tags: []
---

The content of a uniform block is stored in a buffer object, which is effectively nothing more than a reserved piece of global GPU memory. This piece of memory holds no information to what kind of data it holds, so we tell OpenGL what parts of the memory correspond to which uniform variables in the shader.

```glsl
layout (std140) uniform Block {
    float value;
    vec3 vector;
    mat4 matrix;
    float values[3];
    bool boolean;
    int integer;
}
```

To create the buffer, we need to kow the size (in bytes) and the offset of each of the variables to place them in the buffer. The size of each of the elements in OpenGL corresponds to the C++ data types, with vectors being arrays of floats. OpenGL **does not state the spacing between the variables**. This allows hardware to position or pad variables as it sees fit.

For example, some hardware can place a `vec3` adjacent to a `float`, but others might pad the `vec3` into an array of 4 floats before appending the float.

## Shared Memory Layout
GLSL uses a uniform memory layout called shared layout. This is shared because once the offsets are defined by the hardware, they are consistsently shared between multiple programs. With a shared layout, GLSL is allowed to reposition the uniform variables for optimisation as long as the variables' order remains intact. We can query this information using `glGetUniformIndices` and other methods.

## std140 Layout
While a shared layout gives us space-saving optimisations, we need to query the offset for each uniform variable, creating a lot of work. The general practice is to not use the shared layout, but the `std140` layout. This explicitly states the memory layout for each variable type by standardising their respective offsets governed by a set of rules.

Each variable has a base alignment equal to the space a variable takes (including padding) within a uniform block. For each variable, we calculate its aligned offset; the byte offset of a variable from the start of the block. The aligned byte offset must be equal to a multiple of its base alignment.

Because the offset is equal to a multiple of its base alignment, it cannot be packed (directly adjacent). This means that std140 is not the most memory efficient, but it means that memory positions are declarative.

N = 4 bytes
- Scalar (int or bool): Base alignment of N
- Vector: Either 2N or 4N
- Array of Scalars/Vectors: Each element has a base alignment equal to a vec4 (4N), even floats
- Matrices: Stored as a large array of column vectors, where each vector is a vec4
- Struct: Equal to the computed size of its elements according to the previous rules

```glsl
layout (std140) uniform ExampleBlock {
                        //base alignment    //aligned offset
    float value;        // 4                // 0
    vec3 vector;        // 16 (4 floats)    // 16
    mat4 matrix;        // 16               // 32 (column 0)
                        // 16               // 48 (column 1)
                        // 16               // 64 (column 2)
                        // 16               // 80 (column 3)
    float values[3];    // 16               // 96 (element 0)
                        // 16               // 112 (element 1)
                        // 16               // 128 (element 2)
    bool boolean;       // 4                // 144
    int integer;        // 4                // 148

}
```
