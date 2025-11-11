We can give objects reflective or refractive properties when using a cubemapped environment.

## Reflection

![[27-6-reflection.png]]

We can calculate a reflection vector R around the object's normal vector N based on the view direction I. The resulting vector R is then used as a direction vector to index/sample the cubemap, returning a colour value of the environment.

## Refraction

Refraction is the change in direction of light due to the change of the material the light flows through. Refraction is described by Snell's law

![[27-6-refraction.png]]

There is a view vector I, a normal vector N, and a refraction vector R. GLSL has an in-built `refract` function that expects a normal vector, a view direction, and a ratio between both material's refractive indices.

The refractive index determines the amount light distorts / bends in a material where each material has its own refractive index. A list of the most common refractive indices are given in the following table:
- Air: 1.00
- Water: 1.33
- Ice: 1.309
- Glass: 1.52
- Diamond: 2.42

We can calculate the ratio between two materials that the light passes through. In our case, the light/view ray goes from air to glass (if the material is glass-like), so the ratio is:
$$
\frac{1.00}{1.52} = 0.658
$$
So the only thing we'd have to change is the fragment shader.
```glsl
void main() {
	float ratio = 1.00 / 1.52
	vec3 I = normalize(Position - cameraPos);
	vec3 R = refract(I, normalize(Normal), ratio);
	FragColor = vec4(texture(skybox, R).rgb, 1.0);
}
```