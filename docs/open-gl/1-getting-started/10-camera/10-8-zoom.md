The `field of view` largely defines how much we can see of the scene. When the FOV becomes smaller, the scene's projection space becomes smaller. This smaller space is projection over the same NDC, giving the illusion of zooming in.

To zoom in, we use the mouse's scroll wheel. This needs a special callback function.
```c++
glfwSetScrollCallback(window, scrollCallback)

void scrollCallback(GLFWwindow *window, double xOffset, double yOffset) {
	zoom -= (float)yOffset;
	if (zoom < 1.0f)
		zoom = 1.0f;
	if (zoom > 45.0f)
		zoom = 45.0f;
}
```

When scrolling, the `yOffset` tells us the amount we scrolled vertically. We how have to upload the perspective projection matrix to the GPU each frame, but adding the fov/zoom.