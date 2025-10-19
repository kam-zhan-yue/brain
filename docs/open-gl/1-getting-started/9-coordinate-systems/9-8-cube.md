We can extend the 2D plane to a 3D cube. We need a total of 36 vertices (6 faces * 2 triangles * 3 vertices each).

You'll find that some sides of the cube are being drawn over other sides of the cube. This is because OpenGL draws triangle-by-triangle, fragment by fragment. It will overwrite any pixel colour that may have already been drawn there before.

Since OpenGL has no guarantee on the order of triangles being rendered (within the same draw call), some triangles are being drawn on top of each other even though one should clearly be in front of the other.

## Z-Buffer
OpenGL stores depth information in a buffer called the z-buffer that allows OpenGL to decide when to draw a pixel and when not to.

GLFW automatically creates such a buffer (just like how it has a colour buffer that stores the colours of the output image). The depth is stored within each fragment (as the fragment's z value) and whenever the fragment wants to output its colour, OpenGL compares its depth values with the z-buffer. If the current fragment is behind the other fragment, it is discarded. This process is known as depth testing and it is done automatically by OpenGL.

Theoretically, depth testing should solve our problem.

However, if we want to make sure OpenGL actually perform depth testing, we first need to tell OpenGL if we want to enable depth testing as it is disabled by default.

```c++
glEnable(GL_DEPTH_TEST);
```

`glEnable` and `glDisable` functions allow us to enable/disable certain functionality in OpenGL. Since we are using a depth buffer, we also want to clear the depth buffer before each render iteration (otherwise the depth information of the previous frame stays in the buffer). 

```c++
glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
```
## More Cubes!
If we wanted to display 10 of our cubes on screen, we can reuse our code, but make them differ in where it's located in the world with a different rotation. The graphical layout of the cube is already defined, so we don't have to change our buffers or attribute arrays when rendering more objects.

The only thing we'd have to change for each object is its model matrix where we transform the cubes into the world.