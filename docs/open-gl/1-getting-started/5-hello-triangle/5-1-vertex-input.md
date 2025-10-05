To start drawing something, we have to first give OpenGL some input vertex data. OpenGL is a 3D graphics library, so all coordinates that we specify are in 3D. OpenGL doesn't simply transform all your 3D coordinates to 2D pixels on your screen; it only processes 3D coordinates when they're in a specific range between -1.0 and 1.0 on all 3 axes. All coordinates within this so called "normalised device coordinates" range will end up visible on your screen (others won't).

To render a single triangle, we want to specify a total of three vertices with each vertex having a 3D position. We define them in normalised device coordinates in a `float` array.

Because OpenGL works in 3D space, we can render a 2D triangle with each vertex having a `z` coordinate of `0.0`. 

```c++
float vertices[] = {
  -0.5f, -0.5f, 0.0f,
  0.5f, -0.5f, 0.0f,
  0.5f, 0.5f, 0.0f,
};
```

With the vertex data defined, we can send it as input to the first process of the graphics pipeline: the vertex shader. This is done by creating memory on the GPU where we store the vertex data, configure how OpenGL should interpret the memory and specify how to send the data to the graphics card. The vertex shader then processes as many vertices as we tell it to from its memory.

We manage this memory through **vertex buffer objects** (VBO) that can store a large number of vertices in the GPU's memory. The advantage of using those buffer objects is that we can send large batches of data all at once to the graphics card, and keep it there if there's enough memory left, without having to send data one vertex at a time. Sending data to the graphics card from the CPU is relatively slow, so we try to send as much data as possible whenever we can. Once the data is in the graphics card's memory, the vertex shader has almost instant access to the vertices, making it extremely fast.

A vertex buffer object is our first occurrence of an OpenGL object. This buffer has a unique ID corresponding to that buffer, so we can generate one with a buffer ID using the `glGenBuffers` function.

```c++
unsigned int VBO;
glGenBuffers(1, &VBO);
```

OpenGL has many types of buffer objects, and the buffer type of a vertex buffer object is `GL_ARRAY_BUFFER`. OpenGL allows us to bind several buffers at once as long as they have a different buffer type. We can bind the newly created buffer to the `GL_ARRAY_BUFFER` target with the `glBindBuffer` function.

```c++
glBindBuffer(GL_ARRAY_BUFFER, VBO);
```

From that point, any buffer calls we make (on the `GL_ARRAY_BUFFER` target) will be used to configure the currently bound buffer, which is `VBO`. Then we can make a call to the `glBufferData` function that copies the previously defined vertex data into the buffer's memory.

```c++
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW)
```

- `glBufferData` is a function specifically targeted to copy user-defined data into the currently bound buffer.
- The first argument is the type of buffer we want to copy data into: the vertex buffer object currently bound to the `GL_ARRAY_BUFFER` target)
- The second argument specifies the size of the data (in bytes) that we want to pass to the buffer; a simple `sizeof` of the vertex data suffices.
- The third parameter is the actual data we want to send
- The fourth parameter specifies how we want the graphics card to manage the given data. This can take 3 forms:
	- `GL_STREAM_DRAW`: the data is set only once and used by the GPU at most a few times
	- `GL_STATIC_DRAW`: the data is set only once and used many times
	- `GL_DYNAMIC_DRAW`: the data is changed a lot and used many times
- Since the position data of the triangle data does not change, is used a lot, and stays the same for every render call, the usage type is `GL_STATIC_DRAW`.

The vertex data is stored within memory on the graphics card as managed by a vertex buffer object named `VBO`.