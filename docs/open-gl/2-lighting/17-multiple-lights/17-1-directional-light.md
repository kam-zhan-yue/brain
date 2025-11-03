We can define a struct for directional light
```glsl
struct DirectionalLight {
	vec3 direction;
	vec3 ambient;
	vec3 diffuse;
	vec3 specular;
}
```

Then pass the uniform to a function with the prototype:
```
vec3 CalculateDirectionalLight(DirectionalLight light, vec3 normal, vec3 viewDir) {
	vec3 lightDir...
}
```


