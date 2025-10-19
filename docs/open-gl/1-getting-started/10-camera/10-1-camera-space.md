The camera/view space reviews to all the vertex coordinates as seen from the camera's perspective as the origin of the scene. The view matrix transforms all the world coordinates into view coordinates that are relative to the camera's position and direction. To define a camera, we need its position in world space, the direction it's looking at, a vector pointing to the right and a vector pointing upwards from the camera.

![[10-1-camera.png]]

### Camera Position
The camera's position is easy, it is a vector in world space that points to the position. We can set this arbitrarily.

```c++
glm::vec3 cameraPos = glm::vec3(0.0f, 0.0f, 3.0f);
```

### Camera Direction
The next vector is what the camera is pointing at. We can point this to the origin of the scene for now: `(0,0,0)`.

```c++
glm::vec3 cameraTarget = glm::vec3(0.0f, 0.0f, 0.0f);
glm::vec3 cameraDirection = glm::normalize(cameraPos - cameraTarget);
```

## Right Axis
The next vector is a right vector that represents the positive x-axis of the camera space. We can first specify an *up* vector that points upwards in world space. Then we can do a cross product on top of the up vector and the direction vector to get a vector that is perpendicular to both.

```c++
glm::vec3 up = glm::vec3(0.0f, 1.0f, 0.0f);
glm::vec3 cameraRight = glm::normalize(glm::cross(up, cameraDirection));
```

### Up Axis
Retrieving the vector that points to the camera's positive y-axis is just the cross product of the right and direction vector.

```c++
glm::vec3 cameraUp = glm::cross(cameraDirection, cameraRight);
```

This process is known as the Gram-Schmidt process. Using these camera vectors, we can create a `LookAt` matrix.