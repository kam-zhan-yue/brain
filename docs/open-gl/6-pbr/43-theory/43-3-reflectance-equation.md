PBR strongly follows a more specialised version of the render equation known as the reflectance equation. 

![[43-3-equation.png]]

## Radiometry
Radiometry is the measurement of electromagnetic radiation, including visible light. There are several radiometric quantities we can use to measure light over surfaces and directions.

L represents radiance, which quantifies the magnitude or strength of light coming from a single direction. Radiance is a combination of multiple physical quantities.

### Radiant Flux
Radiant flux is the transmitted energy of a light source measured in Watts. Light is a collective sum of energy over multiple different wavelengths, each wavelength associated with a particular colour. The emitted energy of a light source can therefore be thought of as a function of all its different wavelengths. Wavelengths between 390nm to 700nm are considered part of visible light.

![[43-3-wavelengths.png]]o

Radiant flux measures the total area of the wavelength function. We take this input as a light colour encoded in RGB. This encoding comes at a loss of information, but it is generally negligible for visual aspects.

### Solid Angle
Tells us the size of area of a shape projected onto a unit sphere. The area of the projected shape onto this unit sphere is known as the solid angle. 

![[43-3-solid-angle.png]]

Think of being an observer at the centre of a unit sphere and looking in the direction of the shape. The size of the silhouette you make out of it is the solid angle.

### Radiant Intensity
The amount of radiant flux per solid angle, or the strength of a light source over a projected area onto the unit sphere. Given an omnidirectional light that radiates equally in all directions, the radiant intensity can give us its energy over a specific area (solid angle).

![[43-3-radiant-intensity.png]]

### Radiance
Radiance is described as the total observed energy in an area A over the solid angle of a light of radiant intensity.

![[43-3-radiance.png]]

Radiance is a radiometric measure of the amount of light in an area, scaled by the incident (or incoming) angle of the light ø to the surface's normal as cos ø: light is weaker the less directly it radiates onto a surface, and strongest when it is directly perpendicular to the surface.

This is similar to our perception of diffuse lighting as cos directly corresponds to the dot product between the light's direction vector and the surface normal.

```c++
float cosTheta = dot(lightDir, normal);
```

The radiance equation is quite useful as it contains most physical quantities we are interested in.
- If we consider the solid angle and the are to be infinitely small, we can use radiance to measure the flex of a single ray hitting a point in spacae.
- This allows us to calculate the radiance of a single light ray influencing a single fragment point
- We effectively translate the solid angle into a direction vector and the area into a point
- We can then use radiance in our shaders to calculate a single light ray's per-fragment contribution

When it comes to radiance, we generally care about all incoming light onto a point, which is the sum of all radiance known as irradiance. 

## The Equation

![[43-3-equation.png]]

- L in the render equation is the radiance of some point p and a direction vector `wi`.
- cos theta scales the energy based on the light's incident angle, which we find in the reflection equation as `n * wi`
- The reflectance equation calculates the sum of reflected radiance of a point p in the direction `wo` which is the outgoing direction to the viewer. In other words, `Lo` measures the reflected sum of the light's irradiance onto point `p` as viewed from `wo`

### Irradiance
Irradiance is the sum of all incoming radiance we measure light of. This includes all incoming light directions within a hemisphere centred around a point. A hemisphere can be described as half a sphere aligned around a surface's normal n.

To calculate the total of values inside an area or volume, we use an integral over all incoming directions within the hemisphere. This translates to taking the result of small discrete steps of the reflectance equation and averaging their results over the step size. This is known as the Riemann sum.

```c++
int steps = 100;
float sum = 0.0;
vec3 P = ... ;
vec3 Wo = ... ;
vec3 N = ... ;
float dW = 1.0 / steps;
for (int i = 0; i < steps; ++i) {
	vec3 Wi = getNextIncomingLightDir();
	sum += Fr(P, Wi, Wo) * L(P, Wi) * dot(N, Wi) * dW;
}
```

By scaling the steps by `dW`, the sum will equal the total area of volume of the integral function. The `dW` to scale each discrete step can be thought of as `dWi` in the reflectance equation. Mathematically, `dWi` is the continuous symbol over which we calculate the integral, and while it does not directly relate to `dW` in code (Riemann discrete step), it helps to think of it this way.

Taking discrete steps will only give an approximation of the total area of the function. We can increase the accuracy of the Riemann sum by taking more steps.

The reflectance equation sums up the radiance of all incoming light directions `wi` over the hemisphere scaled by `Fr` that hit point `p` and returns the sum of reflected light `Lo` in the viewer's direction. The incoming radiance can come from light source or from an environment map measuring the radiance of every incoming direction.

The only unknown remaining is `Fr`, which is known as the bidirectional reflective distribution function (BRDF), which scales or weighs the incoming radiance based on the surface's material properties.