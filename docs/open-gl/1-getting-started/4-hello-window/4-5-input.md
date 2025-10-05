We want to have some form of input control in GLFW and we can achieve this with several of GLFW's input functions.

```c++
void processInput(GLFWwindow *window) {
	if (glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS) {
		glfwSetWindowShouldClose(window, true);
	}
}
```

- Here we check whether the user has pressed the escape key. If it is not pressed, `glfwGetKey` will return `GLFW_RELEASE`
- If it is pressed, we close GLFW by setting its `WindowShouldClose` property to `true` using `glfwSetWindowShouldClose`
- The next condition check of the main `while` loop will then fail and the application should close.

We need to call `processInput` every iteration of the render loop:
```c++
while (!glfwWindowShouldClose(window)) {
	processInput(window);
	glfwSwapBuffers(window);
	glfwPollEvents();
}
```

- This gives us an easy way to check for specific key presses and react accordingly every frame