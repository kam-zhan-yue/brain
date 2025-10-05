Before we start rendering, we need to tell OpenGL the size of the rendering window so that OpenGL knows how we want to display the data and coordinates with respect to the window. These are passed into the `glViewport` function
```c++
glViewport(0, 0, 800, 600)
```
- The first two parameters set the location of the lower left corder of the window
- The last two parameters set the width and height of the window in pixels
- Behind the scenes, OpenGL uses the data specified by `glViewport` to transform the 2D coordinates it processed to coordinates on your screen. E.g. a processed point of location (-0.5, 0.5) would be mapped to (200, 45) in screen coordinates.
- Processed coordinates in OpenGL are between -1 and 1, so we effectively map from the range (-1 to 1) to (0 to 800) and (0 to 600)

However, the moment a user resizes the window, the viewport should be adjusted as well. We can register a callback function on the window that gets called each time the window is resized. This resized callback function takes the following prototype:

```c++
void framebuffer_size_callback(GLFWwindow* window, int width, int height);
```
- GLFW calls this function and fills in the proper arguments for you to process.

We need to register this function with GLFW

```c++
glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);
```

- When the window is first displayed, the callback is called as well with the resulting window dimensions.
- There are many callback functions we can register to, such as joystick input changes, error messages, etc.
