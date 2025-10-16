To transform the coordinates from one space to the next coordinate space, we'll use several transformation matrices of which the most important are the model, view, and projection matrices.

Our vertex coordinate first start in local space as local coordinates and are further processed to world coordinates, view coordinates, clip coordinates, and eventually end up as screen coordinates.

![[9-1-the-global-picture.png]]

1. Local coordinates are the coordinates of your object relative to its local origin; they're the coordinates your object begins in
2. The next step is to transform the local coordinates to world-space coordinates which are coordinates in respect to a larger world. These coordinates are relative to some global origin of the world, together with many other objects
3. Next we transform the world coordinate to view-space coordinates in such a way that each coordinate is seen from the camera or viewer's point of view
4. After the coordinates are in view space, we want to project them to clip coordinates. Clip coordinates are processed to the `-1.0` and `1.0` range and determine which vertices will end up on the screen. Projection to clip-space coordinates can add perspective if using perspective projection
5. Finally, we transform the clip coordinates to screen coordinates in a process we call viewport transformation that transforms the coordinates from -1.0 to 1.0 to the coordinate range defined in `glViewport`. The resulting coordinates are sent to the rasteriser to turn them into fragments.

The reason we're transforming our vertices into all these different spaces is that some operations make more sense or are easier to use in certain coordinate systems.

E.g. when modifying your object, it makes most sense to do this in local space. When calculating certain operations on the object with respect to the position of other objects, it makes most sense in the world space, etc.
