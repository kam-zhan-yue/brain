A buffer in OpenGL, at its core, is an object that manages a certain piece of GPU memory and nothing more. We give meaning to buffer when binding it to a buffer target. A buffer is only a vertex array buffer when we bind it to `GL_ARRAY_BUFFER`, but we could just as easily bind it to `GL_ELEMENT_ARRAY_BUFFER`. OpenGL internally stores a reference to the buffer per target. Based on the target, it will process the buffer differently.

We can reserve a buffer by calling `glBufferData` with `NULL`, which will allocate memory and not fill it. 

Instead of filling the entire buffer with one function call, we can also fill specific regions of the buffer by calling `glBufferSubData`. This function expects a buffer target, an offset, the size of the data, and the actual data as arguments. We can now give an offset that specifies from where we want to fill the buffer. This allows us to insert/update only certain parts of the buffer's memory. The buffer should have enough allocated memory, so a call to `glBufferData` is necessary.

```c++
// Range: [24, 24 + sizeof(data)]
glBufferSubData(GL_ARRAY_BUFFER, 24, sizeof(data), &data);
```

Another method to get data into a buffer is to ask for a pointer to the buffer's memory and directly copy the data in memory. By calling `glMapBuffer`, OpenGL returns a pointer to the currently bound buffer's memory.

```c++
float data[] = {
	0.5f, 1.0f, -0.35f
};

glBindBuffer(GL_ARRAY_BUFFER, buffer);
void *ptr = glMapBuffer(GL_ARRAY_BUFFER, GL_WRITE_ONLY);
memcpy(ptr, data, sizeof(data));
glUnmapBuffer(GL_ARRAY_BUFFER);
```

By telling OpenGL we're finished with the pointer operations via `glUnmapBuffer`, OpenGL knows you're done. By unmapping, the pointer becomes invalid and the function returns `GL_TRUE` if OpenGL was able to map your data successfully to the buffer.

Using `glMapBuffer` is useful for directly mapping data to a buffer, without first storing it in temporary memory. This is like reading data from a file and copying it into the buffer's memory.