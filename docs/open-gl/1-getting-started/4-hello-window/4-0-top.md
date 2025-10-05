```c++
#include <glad/glad.h>
#include <GLFW/glfw3.h>
#include <iostream>

int main() {
  // Init GLFW and set the context variables
  glfwInit();
  glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
  glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
  glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
  #ifdef __APPLE__
  glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);
  #endif
}
```
## Notes
 - Include GLAD before GLFW. The include file for GLAD includes the required OpenGL headers behind the scenes.
 - The first argument of `glfwWindowHint` tells us what option we want to configure, where we can select the option from a large enum of possible options prefixed with `GLFW_`.
 - The second argument is an integer that sets the value of our option. A list of all the possible options and its corresponding values can be found at GLFW's window documentation.
 - We tell GLFW that 3.3 is the OpenGL version we want to use. This way, GLFW can make the proper arrangements when creating the OpenGL context. This ensures when a user doesn't have the proper OpenGL version, then GLFW fails to run.
 - We also tell GLFW we want to explicitly use the core-profile. This means we'll get access to a smaller subset of OpenGL features without backwards-compatible features we no longer need.
 - We need a special command for MacOS

```c++
// Create a window object
GLFWwindow* window = glfwCreateWindow(800, 600, "LearnOpenGL", NULL, NULL);
if (window == NULL) {
std::cout << "Failed to create GLFW window" << std::endl;
glfwTerminate();
return -1;
}
glfwMakeContextCurrent(window);
```

- The `glfwCreateWindow` function requires the window width and height. The third argument allows us to create a name.
- The function returns a `GLFWwindow` object that we'll need later
- After that, we tell GLFW to make the context of our window the main context on the current thread
