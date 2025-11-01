Light sources also have different intensities for their ambient, diffuse, and specular components respectively. We want to specify intensity vectors for each of the lighting components. If we visualise `lightColour` as `vec3(1.0)`, the code would look like:

```glsl
vec3 ambient = vec3(1.0) * material.ambient;
vec3 diffuse = vec3(1.0) * (diff * material.diffuse);
vec3 specular = vec3(1.0) * (spec * material.specular);
```

Each of the material property of the object is returned with the full intensity for each of the light's components. These `vec3(1.0)` values can be influenced individually as well for each light source and that is what we usually want. Right now the ambient component of the object is fully influencing the colour of the cube. The ambient component shouldn't really have such a big impact on the final colour so we can restrict the ambient colour by setting the light's ambient intensity to a lower value.

```glsl
vec3 ambient = vec3(0.1) * material.ambient;
```

We can influence the diffuse and specular intensity of the light source in the same way. 

```glsl
struct Light {
	vec3 position;
	vec3 ambient;
	vec3 diffuse:
	vec4 specular;
}

uniform Light light;
```

A light source has a different intensity for its `ambient`, `diffuse`, and `specular` components. The ambient light is usually set to a low intensity because we don't want the ambient colour to be too dominant. The diffuse component is usually set to the exact colour ew'd like a light to have, often a bright white colour. The specular component is kept at `vec3(1.0)` shining at full intensity.