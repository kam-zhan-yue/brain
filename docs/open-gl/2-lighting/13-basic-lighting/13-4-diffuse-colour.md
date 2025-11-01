We have the normal vector, but we still need the light's position vector and the fragment's position vector. Since the light's position is a single static variable, we can declare it as a uniform in the fragment shader.

```glsl
uniform vec3 lightPos;
```

And the update the uniform in the render loop (our outside).

```c++
shader.setVec3("lightPos", lightPos);
```

The last thing we need is the actual fragment's position. We do all the lighting calculations in world space, so we want a vertex position that is in world space first. We can accomplish this by multiplying the vertex position attribute with the model matrix only to transform it to world space coordinates.

```glsl
out vec3 FragPos;
out vec3 Normal

void main() {
	gl_Position = projection * view * model * vec4(aPos, 1.0);
	FragPos = vec3(model * vec4(aPos, 1.0));
	Normal = aNormal;
}
```

Then we add this to the cube shader
```glsl
in vec3 FragPos;
```

This `in` variable will be interpolated from the 3 world position vectors of the triangle to form the `FragPos` vector that is the per-fragment world position. 

### Calculating
The first thing to calculate is the direction vector between the light source and the fragment's position.

```glsl
vec3 norm = normalize(Normal);
vec3 lightDirection = normalize(lightPos - FragPos)
```

Next, we need to calculate the diffuse impact of the light on the current fragment by taking the dot product between the `norm` and `lightDirection` vectors. The resulting value is multiplied with the light's colour to get the diffuse component, resulting in a darker diffuse component the greater the angle between both vectors.

```glsl
float diff = max(dot(norm, lightDirection), 0.0);
vec3 diffuse = diff * lightColour;
```

If the angle is greater than 90 degrees, the dot product becomes negative, so we ensure a minimum here.

Now that we have an ambient and a diffuse component, we add both colours:
```glsl
vec3 result = (ambient + diffuse) * objectColour;
FragColor = vec4(result, 1.0);
```