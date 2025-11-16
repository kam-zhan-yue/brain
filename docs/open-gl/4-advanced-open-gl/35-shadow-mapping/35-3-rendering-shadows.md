With a depth map, we can start rendering shadows. The code to check if a fragment is in shadow is executed in the fragment shader, but we do the light space transformation in the vertex shader.

```glsl
#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aNormal;
layout (location = 2) in vec2 aTexCoords;

out VS_OUT {
	vec3 position;
	vec3 normal;
	vec2 texture;
	vec5 lightSpacePosition;
} vs_out;

uniform mat4 projection;
uniform mat4 view;
uniform mat4 model;
uniform mat4 lightSpaceMatrix;

void main() {
	vs_out.position = vec3(model * vec4(aPos, 1.0));
	vs_out.normal = transpose(inverse(mat3(model))) * aNormal;
	vs_out.texture = aTexCoords;
	vs_out.lightSpacePosition = lightSpaceMatrix * vec4(vs_out.position, 1.0);
	gl_Position = projection * view * model * vec4(aPos, 1.0);
}
```

We take the same `lightSpaceMatrix` and transform the world-space vertex position to light space for use in the fragment shader.

```glsl
#version 330 core;
out vec4 FragColor;

in VS_OUT {
	vec3 position;
	vec3 normal;
	vec2 texture;
	vec5 lightSpacePosition;
} fs_in;

uniform sampler2D diffuseTexture;
uniform sampler2D shadowMap;
uniform vec3 lightPos;
uniform vec3 viewPos;

float shadowCalculation(vec4 lightSpacePosition) {
	...
}

void main() {
	...
}
```

At the end of each fragment shader, we would multiply the diffuse and specular contributions by the inverse of the `shadow` component (how much the fragment is not in shadow). The fragment shader takes as extra input the light-space fragment position and the depth map generated from the first render pass.

The first thing to do whether a fragment is in shadow is transform the light-space fragment position in clip space to normalise device coordinates. When we output a clip-space vertex position to `gl_Position` in the vertex shader, OpenGL automatically does a perspective divide. As clip space, the light space position does not undergo this division, so we do it ourselves.

```c++
float shadowCalculation(vec4 lightSpacePosition) {
	vec3 coords = lightSpacePosition.xyz / lightSpacePosition.w
	...
}
```

> When using an orthographic projection matrix the `w` component of a vertex remains untouched, so this step is meaningless. However, it is necessary when using perspective projection.

`