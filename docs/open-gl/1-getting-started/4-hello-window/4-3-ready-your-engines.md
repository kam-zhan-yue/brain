We don't want the application to draw a single image and then immediately quit and close the window. We want the application to keep drawing images and handling user input until the program has been explicitly told to stop.

For this reason, we need to make a while loop (the render loop) that keeps on running until we tell GLFW to stop.

```c++
while (!glfwWindowShouldClose(window)) {
	glfwSwapBuffers(window);
	glfwPollEvents();
}
```

- The `glfwWindowShouldClose` function checks at the start of each loop iteration if GLFW has been instructed to close. If so, the function returns `true` and the render loop stops running, after which we can close the application.
- The `glfwPollEvents` function checks if any events are triggered (like keyboard input or mouse movement events), updates the window state, and calls the corresponding functions
- The `glfwSwapBuffers` will swap the colour buffer (a large 2D buffer that contains colour values for each pixel in GLFW's window) that is used to render this render iteration and show it as output to the screen.

> Double buffer: When an application draws in a single buffer, the resultant iage may display flickering issues. This is because the resultant output image is not drawn in an instant, but drawn pixel by pixel and usually from left to right and top to bottom.

> Because this image is not displayed at an instant to the user while still being rendered to, the result may contain artifacts.

> To circumvent these issues, windowing applications apply a double buffer for rendering. The **front** buffer contains the final output image that is shown at the screen while all the rendering commands draw to the **back** buffer. As soon as all the rendering commands are finished, we **swap** the back buffer to the front buffer so the image can be displayed without still being rendered to, removing all the aforementioned artifacts.

