We can use a set of vertex data with a counter-clockwise winding order to allow for face culling.

```c++
glEnable(GL_CULL_FACE);
```

From this point on, all the faces that are not front-faces are discarded. This only works on closed shapes like a cube. We would have to disable face culling again when drawing grass leaves since their front and back faces should be visible.

OpenGL allows us to change the type of face we want to cull.

```c++
glCullFace(GL_FRONT);
```
- `GL_BACK`: culls only the back faces
- `GL_FRONT`: culls only the front faces
- `GL_FRONT_AND_BACK`: culls both the front and back faces

The initial value of `glCullFace` is `GL_BACK`. 

We can also tell OpenGL we'd rather prefer clockwise faces as the front-faces via `glFrontFace`.

```c++
glFrontFace(GL_CCW); // ccw = counter clockwise
```


We can reverse the winding order by telling OpenGL that the front faces are now determined by a clockwise ordering instead of a counter-clockwise ordering:

```c++
glEnable(GL_CULL_FACE);
glCullFace(GL_BACK);
glFrontFace(GL_CW);
```

We can make the same effect by culling front faces with CCW.
