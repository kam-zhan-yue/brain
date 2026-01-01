Looking back at the final reflectance equation, we mostly know what is going on, but we still do not know how to represent irradiance (the total radiance), L, of the scene. The irradiance means radiant flux over a given solid angle. In our case, we assumed the solid angle to be infinitely small in which radiance measures the flux of a light source over a single light ray or direction vector.

![[43-4-final-equation.png]]

## Point Light Example
Imagine a single point light with a radiant flux of (23.47, 21.31, 20.79) as an RGB triplet. The radiant intensity of this light source equals its radiant flux at all outgoing direction rays. However, when shading a specific point p on a surface, of all possible incoming light directions over its hemisphere, only one incoming direction vector directly comes from the light source.

As we only have a single light source in our scene, assumed to be a single point in space, all other possible incoming light directions have zero radiance observed over the surface point.

![[44-0-point-light.png]]

If we assume that light attenuation does not affect the point light source, the radiance of the incoming light ray if the same regardless of where we position the light (since the power of the light doesn't dim). Because the point light has the same radiant intensity regardless of the angle we look at it, we can model its radiant flux as a constant vector.

However, radiance also takes a position *p* as input and as any realistic point light source takes light attenuation into account, the radiant intensity of the point light source is scaled by some measure of distance between the point and the light source. As extracted from the original equation, the result is scaled by the dot product between the surface normal and the incoming light direction.

In more practical terms: in the case of a direct point light, the radiance function L measures the light colour, attenuated over its distance to *p* and scaled by `n * wi`, but only over the single light ray `wi` that hits `p` which equals the light's direction vector from `p`.

```c++
vec3 lightColour = vec3(23.47, 21.31, 20.79);
vec3 wi = normalize(lightPos - fragPos);
float cosTheta = max(dot(N, Wi), 0.0);
float attenuation = calculateAttenuation(fragPos, lightPos);
vec3 radiance = lightColour * attenuation * cosTheta;
```

This is exactly the same as how we've been calculating diffuse lighting so far. 

We calculate radiance similarly for other types of light sources.
- A directional light has a constant without an attenuation factor
- A spot light would not have constant radiant intensity, but one that is scaled by the forward direction vector of the spotlight

This brings us back to the integral over the surface's hemisphere. Since we know all the single locations of all the contributing light sources while shading a single surface point, it is not required to try and solve the integral. We can directly take the number of light sources and calculate their total irradiance, given that each light source has only a single light direction that influences the surface's radiance.

This makes PBR on direct light sources relatively simple as we effectively only have to loop over the contributing light sources.