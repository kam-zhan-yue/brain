A light source that *casts* light upon objects is called a light caster. There are several types of light casters.

When a light source is far away, the light rays coming from the light source are close to parallel to each other. It looks like all the light rays are coming from the same direction, regardless of where the object and/or viewer is. When a light source is modelled to be *infinitely* far away, it is called a **directional light** since all its light rays have the same direction; it is independent of the location of the light source.

Because all the light rays are parallel, it does not matter how each object relates to the light source's position since the light direction remains the same for each object in the scene. Because the light's direction vector stays the same, the lighting calculations will be similar for each object in the scene.

We can model directional light by defining a light direction vector instead of a position vector. The shader calculations remain mostly the same except this time we directly use the light's `direction` vector instead of calculating the `lightDir` vector using the light's `position` vector.

```glsl
struct Light {
	// vec3 position // no longer necessary
	vec3 direction;
	vec3 ambient;
	...
}

vec3 lightDir = normalize(-light.direction);
```

We first negate the `light.direction` vector. The lighting calculations we used so far expect the direction to be from the fragment towards the light source. The resulting `lightDir` vector is then use as before in the diffuse and specular computations.

> We've been passing the light's position and direction vectors as `vec3`s for a while, but some people prefer to keep all the vectors defined as `vec4`. When defining position vectors as `vec4` it is important to set the `w` component to `1.0` so translation and projection are properly applied. However, when defining a direction vector as a `vec4` we don't want translations to 