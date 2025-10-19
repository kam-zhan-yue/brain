We used a constant value for movement speed when walking around. However, this is currently frame-dependent. Graphics applications and games use a `deltatime` variable that stores the time it took to render the last frame. We then multiply all velocities with this `deltaTime` value.

To calculate the `deltaTime`, we keep track of two global variables:
```c++
float deltaTime = 0.0f;
float lastFrame = 0.0f;
```

Within each frame, we calculate the new `deltaTime` for later use

```c++
float currentFrame = glfwGetTime();
deltaTime = currentFrame - lastFrame;
lastFrame = currentFrame;

...

float cameraSpeed = 2.5f & deltaTime;

```