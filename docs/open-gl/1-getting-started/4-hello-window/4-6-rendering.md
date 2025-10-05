We want to place all of the rendering commands in the render loop, since we want to execute all the rendering commands each frame. This looks like

```c++
while (!glfwWindowShouldClose(window)) {
	// input
	processInput(window);
	
	// rendering commands
	...
	
	// check events and swap the buffers
	glfwPollEvents();
	glfwSwapBuffers(window)
}
```

To test things, we can clear the screen with a colour of our choice. At the start of the frame, we want to clear the screen, otherwise we would still see the results from the previous frame. This is done using `glClear`, where we pass in buffer bits to specify which buffer we would like to clear. The possible bits we can set are `GL_COLOR_BUFFER_BIT`, `GL_DEPTH_BUFFER_BIT`, and `GL_STENCIL_BUFFER_BIT`.

Right now, we only care about the colour values, so we only clear the colour buffer.

```c++
glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
glClear(GL_COLOR_BUFFER_BIT);
```

Note that we also specify the colour to clear the screen with `glClearColor`. Whenever we call `glClear` and clear the colour buffer, the entire colour buffer will be filled with the colour as configured by `glClearColor`.

> The `glClearColor` is a *state-setting* function and `glClear` is a *state-using* function in that it uses the current state to retrieve the clearing colour from.