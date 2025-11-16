To use MSAA in OpenGL, we use a buffer that is able to store more than one sample value per pixel. This is known as a multisample buffer.

Most windowing systems are able to provide a multisample buffer instead of a default buffer. GLFW gives us this functionality and we just need to *hint* GLFW that we want to use a multisample buffer with N buffers.

```c++
glfwWindowHint(GLFW_SAMPLES, 4);
```

When we call `glfwCreateWindow`, we create a rendering window but with a buffer containing 4 subsamples per screen coordinate. This increases the size of the buffers by 4.

On most OpenGL drivers, multisampling is enabled by default, but we can still call

```c++
glEnable(GL_MULTISAMPLE);
```

