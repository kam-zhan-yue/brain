One of the properties of light is that it can scatter and bounce in many directions, reaching spots that aren't directly visible. Light can thus reflect on other surfaces and have an indirect impact on the lighting of an object. Algorithms that take this into consideration are called *global illumination algorithms*, but these are complicated.

Adding ambient lighting to a scene is easy. We take the light's colour, multiply it with a small constant ambient factor, multiply this with the object's colour, and use that as the fragment's colour in the object shader.

```c++
void main() {
	float ambientStrength = 0.1;
	vec3 ambient = ambientStrength * lightColour;
	vec3 result = ambient * objectColour;
	FragColor = vec4(result, 1.0);
}
```
