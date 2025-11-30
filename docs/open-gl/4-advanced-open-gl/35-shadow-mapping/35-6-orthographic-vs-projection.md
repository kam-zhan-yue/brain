There is a difference between rendering the depth map with an orthographic or a projection matrix. An orthographic projection matrix does not deform the scene with perspective so all view / light rays are parallel. This makes it a great projection matrix for directional lights. A perspective projection matrix however does deform all vertices based on perspective which gives different results.

![[35-6-projection.png]]

Perspective projections make most sense for light sources that have actual locations, unlike directional lights. Perspective projections are most often used with spotlights and point lights. while orthographic projections are used for directional lights.

Another subtle difference with using a perspective projection matrix is that visualising the depth buffer will often give an almost completely white result. This happens because with perspective projection, the depth is transformed to non-linear depth values with most of its noticeable range close to the near plane. To be able to properly view the depth values as we did with the orthographic projection you first want to transform the non-linear depth values to linear.

```glsl
#version 330
out vec4 FragColor;
in vec2 TexCoords;

uniform sampler2D depthMap;
uniform float nearPlane;
uniform float farPlane;

float linearizeDepth(float depth) {
	float z = depth * 2.0 - 1.0; // back to NDC
	return (2.0 * nearPlane * farPlane) / (farPlane + nearPlane - z * 
}
// too lazy to finish this.
```