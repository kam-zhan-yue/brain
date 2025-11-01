A normal vector is a vector that is perpendicular to the surface of a vertex. Since a vertex has no surface, we retrieve a normal vector by using its surrounding vertices to figure out the surface of the vertex.

We use a little trick to calculate the normal vectors of a cube's surface with the cross product. Since a 3D cube is not a complicated shape, we can add this normal data to the vertex data. 

> Imagine that the normals are vectors perpendicular to each plane's surface.

Each vertex creates a triangle, so the normal would be the normal to that triangle.

We can pass this into the vertex shader to pass to the fragment shader.

```glsl
#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aNormal;

out vec3 Normal

void main() {
	gl_Position = projection * view * model * vec4(aPos, 1.0);
	Normal = aNormal;
}
```
