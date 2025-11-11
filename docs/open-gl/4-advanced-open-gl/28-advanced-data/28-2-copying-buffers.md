Once buffers have been filled with data, we can share that data with other buffers or copy the buffer's content to another buffer. The `glCopyBufferSubData` function allows us to copy the data from one buffer to another.

The function prototype is:

```c++
void glCopyBufferSubData(GLenum readtarget, GLenum writetarget, GLintptr readoffset, GLintptr writeoffset, GLsizeiptr size);
```

- We can set the `readtarget` and `writetarget` to different buffer types such as `VERTEX_ARRAY_BUFFER` and `VERTEX_ELEMENT_ARRAY_BUFFER`
- To read and write data into two different buffers that are both vertex array buffers, we can't bind two buffers at the same time to the buffer target.
- For the above, we use `GL_COPY_READ_BUFFER` and `GL_COPY_WRITE_BUFFER`. We then bind the buffers of our choice to these new buffer targets and set those targets as the `readtarget` and `writetarget` argument.

`glCopyBufferSubData` then reads data of a given size from a given readoffset and writes it into the writetarget with a writeoffset.

```c++
glBindBuffer(GL_COPY_READ_BUFFER, vbo1);
glBindBuffer(GL_COPY_WRITE_BUFFER, vbo2);
glCopyBufferSubData(GL_COPY_READ_BUFFER, GL_COPY_WRITE_BUFFER, 0, 0, 8 * sizeof(float));
```

We can also do this by only binding the write target
```c++
float vertexData[] = {};
glBindBuffer(GL_ARRAY_BUFFER, vbo1);
glBindBuffer(GL_COPY_WRITE_BUFFER, vbo2);
glCopyBufferSubData(GL_ARRAY_BUFFER, GL_COPY_WRITE_BUFFER, 0, 0, ...);
```

