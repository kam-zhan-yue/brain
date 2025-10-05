As soon as we exit the render loop, we would like to properly clean/delete all of GLFW's resources that were allocated. We can do this with `glfwTerminate`.

```c++
glfwTerminate();
return 0;
```

This will clean up all the resources and properly exit the application. 