Similar to directional lights, we want to define the point light struct.

```glsl
struct PointLight {
	vec3 position;
	float constant;
	float linear;
	float quadratic;
	vec3 ambient;
	vec3 diffuse;
	vec3 specular;
}

#define NUM_POINT_LIGHTS 4
uniform PointLight pointLights(NUM_POINT_LIGHTS)
```

We need to use a pre-processor directive in GLSL to define the number of point lights we want to have in our scene. 