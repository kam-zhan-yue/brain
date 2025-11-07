When we define a set of triangles, we define them in a certain winding order that is either clockwise or counter-clockwise.

Each triangle consists of 3 vertices and we specify them in a winding order.

![[25-1-winding-order.png]]

We first define vertex 1 and from there, we can choose whether the next vertex is 2 or 3, determining the winding order of the triangle. For example:

```c++
float vertices[] = {
	// clockwise
	vertices[0],
	vertices[1],
	vertices[2],
	// anti-clockwise
	vertices[0],
	vertices[2],
	vertices[1],
}
```

Each set of 3 vertices that form a triangle primitive contains a winding order. OpenGL uses this information when rendering primitives to determine if a triangle is front-facing or back-facing.

By default, triangles with counter-clockwise vertices are processed as front-facing triangles.

When you define your vertex order, you visualise the corresponding triangle as if it was facing you, so each triangle that you're specifying should be counter-clockwise as if you're directly facing that triangle.

The actual winding order is calculated at the rasterisation stage, so when the vertex shader has already run. The vertices are then seem from the viewer's point of view.

All the triangle vertices that the user is facing are in the correct winding order as we specified them, but the vertices of the other triangle at the other side of the cube are now rendered in such a way that their winding order becomes reversed. The result is that the triangles we're facing are seen as front-facing triangles and the triangles at the back are seen as back-facing triangles.

![[25-1-winding-order-cube.png]]

In the vertex data, we defined both triangles in counter-clockwise order. However, from the viewer's direction, the back triangle is rendered clockwise! Even though we specified the back triangle in counter-clockwise order, it is rendered in clockwise order. This is how OpenGL culls non-visible faces.
