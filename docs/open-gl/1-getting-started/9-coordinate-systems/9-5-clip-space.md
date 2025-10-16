At the end of each vertex run, OpenGL expects the coordinates to be within a specified range and any coordinate that falls outside of this range is clipped. Coordinates that are clipped are discarded, so the remaining coordinates end up as fragments visible on your screen. This is also where clip space gets its name from.

Because specifying all visible coordinates to be within the range -1.0 and 1.0 isn't really intuitive, we specify our own coordinate set and convert those back to NDC.

To transform vertex coordinates from view to clip space, we define a so called projection matrix that specifies a range of coordinates (e.g. -1000 and 1000) in each dimension. The projection matrix then transform coordinates within this specified range to NDC. All coordinates outside this range will not be mapped between -1.0 and 1.0 and are clipped.

> E.g. a coordinate of (1250, 500, 750) would not be visible since the x coordinate is out of range and is clipped

This viewing box of a projection matrix is called a *frustum* ad each coordinate that ends up inside this frustum will end up on the user's screen. The total process to convert coordinates within a specified range to NDC that can easily be mapped to 2D view-space coordinates is called projection since the projection matrix projects 3D coordinates to the easy-to-map-to-2D NDC.

Once all the vertices are transformed to clip space, a final operation called perspective division is performed where we divide the x, y, and z components of the position vectors by the vector's homogenous w component. Perspective division is what transforms the 4D clip space coordinates to 3D NDC. This step is performed automatically at the end of the vertex shader step.

The projection matrix to transform view coordinates to clip coordinates usually takes two different forms, where each from defines its own unique frustum.
## Orthographic Projection

An orthographic projection matrix defines a cube-like frustum box that defines the clipping space where each vertex outside this box is clipped. When creating an orthographic projection matrix, we specify the width, height, and length of the visible frustum. All the coordinates inside this frustum will end up within the NDC range after transformed by its matrix and won't be clipped.

The frustum looks a bit like a container.

![[9-5-orthogarphic-projection.png]]

The frustum defines the visible coordinates and is specified by a width, a height, and a near and far plane. Any coordinate in front of the near plane is clipped and the same for coordinates behind the far plane.

The orthographic frustum directly maps all coordinates inside the frustum to normalised device coordinates without any special side effects since it wont' touch the w component.

To create an orthographic projection matrix, we can do:

```c++
glm::ortho(0.0f, 800.0f, 0.0f, 600.0f, 0.1f, 100.0f);
```

The first two parameters specify the left and right coordinate of the frustum and the third and fourth parameter specify the bottom and top part of the frustum. With those 4 points, we define the size of the near and far planes, and the 5th and 6th parameters define the distances between the near and far planes. This specific projection matrix transforms all coordinates between these x, y, and z range values to normalised device coordinates.

An orthographic projection matrix directly maps coordinates to the 2D plan that is your screen, but in reality a direct projection produces unrealistic results since the projection doesn't take perspective into account.

## Perspective Projection

With perspective, objects that are farther away will appear much smaller. Perspective is especially noticeable when looking down at the end of an infinite motorway or railway. To mimic the vanishing line, we use a perspective projection matrix. The projection matrix maps a given frustum range to clip space, but also manipulates the `w` value of each vertex coordinate in such a way that the further a vertex coordinate is from the viewer, the higher this `w` component becomes.

Once the coordinates are transformed to clip space, they are in the range -w to w and anything outside is clipped. OpenGL requires that the visible coordinates are in clip space, so perspective division is applied as:
$$
out =
\left(
\begin{array}{cc}
x / w \\
y / w \\
z / w \\
\end{array}
\right)
$$

Each component of the vertex coordinate is divided by its `w` component giving smaller vertex coordinates the further away a vertex is from the viewer. This is another reason why the `w` component is important, since it helps with perspective projection.

A perspective projection matrix can be created in GLM as follows:
```c++
glm::mat4 proj = glm::perspective(glm::radians(45.0f), (float)width / (float)height, 0.1f, 100.0f);
```

What `glm::perspective` does is create a large *frustum* that defines the visible space. A perspective frustum can be visualised as a non-uniformly shaped box where each coordinate in the box will be mapped to a point in clip space.

![[9-5-perspective-projection.png]]

- The first parameter is the `fov` (field of view) and sets how large the viewspace is
- The second parameter sets the aspect ratio which is calculated by dividing the viewport's width by its height
- The third and fourth parameter set the near and far plane of the frustum

When using orthographic projection, each of the vertex coordinates are directly mapped to clip space without any perspective division. Because the orthographic projection doesn't use perspective projection, objects farther away do not seem smaller, which produces a weird visual output.

Orthographic projection is mainly used for 2D renderings and architectural/engineering applications.