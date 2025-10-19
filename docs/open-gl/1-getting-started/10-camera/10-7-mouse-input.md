The yaw and pitch values are obtained from mouse (or controller) movement where the horizontal mouse-movement affects the yaw and vertical mouse-movement affects the pitch. The idea is to store the last frame's mouse positions and calculate in the current frame how much the mouse values changed.

The higher the horizontal or vertical difference, the more we update the pitch or yaw value and thus the more the camera should move.

## Cursor Capture
First, we tell GLFW to hide the cursor and capture it. Capturing a cursor means that once the application has focus, the mouse cursor stays within the centre of the window.

```c++
glfwSetInputMode(window, GLFW_CURSOR, GLFW_CURSOR_DISABLED);
```

After this call, wherever we move the mouse, it won't be visible and it should not leave the window.

To calculate the pitch and yaw values, we need to tell GLFW to listen to the mouse-movement events.

```c++
void mouse_callback(GLFWwindow* window, double xpos, double ypos);

...
glfwSetCursorPosCallback(window, mouse_callback)
```

When handling mouse input for a fly style camera, we need to:
1. Calculate the mouse's offset since the last frame
2. Add the offset values to the camera's yaw and pitch values
3. Add some constraints to the minimum/maximum pitch values
4. Calculate the direction vector

### Calculating Mouse Offset
The first step is to calculate the offset of the mouse since last frame. We initialise the mouse positions to be in the centre of the screen.
```c++
float lastX = 400, lastY = 300;
```

Then in the mouse's callback function, we calculate the offset movement between the last and current frame.

```c++
float xOffset = xPos - lastX;
float yOffset = lastY - yPos; // reversed as y ranges bottom to top
lastX - xPos;
lastY = yPos;

// sensitivity to calm down the mouse movement
const float sensitivity = 0.1f;
xOffset *= sensitivity;
yOffset *= sensitivity;
```

### Adding Offset Values
Next we add the values to the globally declared `pitch` and `yaw` values.
```c++
yaw += xOffset;
pitch += yOffset;
```

### Constraints to Pitch Values
We want to add constraints to the camera so that users won't be able to make weird camera movements (which causes a LookAt flip once direction vector is parallel to the world's up direction). The pitch should be constrained in such a way that users won't be able to look higher than 89 degrees and not below -89 degrees. At 90 degrees, the LookAt flip occurs.

```c++
if (pitch > 89.0f)
	pitch = 89.0f;
else if (pitch < -89.0f)
	pitch = 89.0f;
```

### Calculate the Direction Vector
Using the previous formula, we can then do:
```c++
glm::vec3 direction;
direction.x = cos(glm::radians(yaw)) * cos(glm::radians(pitch));
direction.y = sin(glm::radians(pitch))
direction.z = sin(glm::radians(yaw)) * cos(glm::radians(pitch));
```

The computed direction vector then contains all the rotations calculated from the mouse's movement. 

If you run the code, you'll notice that the camera makes a large sudden jump whenever the window first receives focus of the mouse cursor. The cause for this sudden jump is that as soon as your cursor enters the window, the mouse callback function is called with an xPos and a yPos position equal to the location your mouse entered the screen from.

This is often a position that is significantly far away from the centre of the screen, resulting in large offsets and thus a large movement jump. We can circumvent this issue by defining a global `bool` variable to check if this is the first time we receive mouse input.