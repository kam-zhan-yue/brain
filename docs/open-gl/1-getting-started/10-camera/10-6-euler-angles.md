Euler angles are 3 values that can represent rotation in 3D. There are three Euler angles: *pitch, yaw, roll*.

![[10-6-euler-angles.png]]

- The pitch is the angle that depicts how much we're looking up or down
- The yaw represents the magnitude we're looking to the left or right
- The roll represents how much we roll (usually in space flights)

Each of the Euler angles are represented by a single value and we can calculate any rotation value in 3D.

Our camera system only cares about the pitch and yaw values. Given a pitch and a yaw value, we can convert them into a 3D vector that represents a new direction vector.

Yaw is essentially a rotation on the x-z axis. (as if looking down on the y-axis)
![[10-6-yaw.png]]
```c++
glm::vec3 direction;
direction.x = cos(glm::radians(yaw));
direction.z = sin(glm::radians(yaw));
```

Pitch is essentially a rotation on the y-axis around the xz-plane.
![[10-6-pitch.png]]
```c++
direction.y = sin(glm::radians(pitch));
```

However, from the pitch triangle, we can see that the xz sides are influenced by `cos`, so we need to include this as part of the direction vector. The final direction vector is:
```c++
direction.x = cos(glm::radians(yaw) * cos(glm::radians(pitch));
direction.y = sin(glm::radians(pitch));
direction.z = sin(glm::radians(yaw) * cos(glm::radians(pitch));
```

This gives us a formula to convert yaw and pitch values to a 3-dimensional direction vector that we can use for looking around.

We setup the scene world so everything's positioned in the direction of the negative z-axis. However, if we look at the x and z yaw triangle, we see that an angle of 0 results in the camera's direction to point to the positive axis. To make sure that the camera points towards the negative z-axis by default, we can give the yaw a default of a 90 degree clockwise rotation.

```c++
yaw = -90.0f;
```