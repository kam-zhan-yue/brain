The microfacet approximation employs a form of energy conservation: outgoing light energy should never exceed the incoming light energy (excluding emissive surfaces).

Specular reflection area increases as roughness increases, but its brightness also decreases. If the specular intensity were to be the same at each pixel, regardless of the size of the specular shape, the rougher surfaces would emit much more energy and violate the energy conservation principle. This is why we see specular reflections more intensity on smooth surfaces and more dimly on rough surfaces.

## Refraction and Reflection
For energy conservation to hold, we need to make a clear distinction between diffuse and specular light. The moment a light ray hits a surface, it gets split in refraction and reflection parts.
- The reflection part is the light that gets directly reflected and doesn't enter the surface. This becomes specular lighting
- The refraction part is the remaining light that enters the surface and gets absorbed. This becomes diffuse lighting.

There are some nuances here as refracted light doesn't immediately get absorbed. Light continues to move forward through the material until it loses all of its energy. Each collision in the material causes the material to absorb some, or all, of the light's energy.

![[43-2-light-model.png]]

Generally, not all energy is absorbed and the light will continue to scatter in a random direction until its energy is depleted or it leaves the surface again. Light rays re-emerging out of the surface contributes to the surface's diffuse colour.

In PBR, we make the simplifying assumption that all refracted light is absorbed and scattered at a very small area of impact, ignoring the effect of scattered light rays that would've exited the surface at a distance. Specific shader techniques that do that this into account are known as subsurface scattering.

## Metallic Surfaces
Metallic surfaces react different to light compared to non-metallic surfaces (also known as dielectrics). Metallic surfaces follow the same principles of reflection and refraction, but all refracted light gets directly absorbed without scattering. This means metallic surfaces only leave reflected or specular light; metallic surfaces have no diffuse colour. Because of this apparent distinction between metals and dielectrics, they are treated differently. 

## Mutual Exclusivity
Reflected and refracted light are mutually exclusive. Whatever light energy gets reflected will no longer be absorbed by the material itself. Thus, the energy left to enter the surface as refracted light is directly the energy after we've taken reflection into account.

We preserve this energy conserving relation by calculating the specular fraction that amounts to the percentage of incoming light energy that is reflected, then calculating the refraction.

```c++
float kS = calculateSpecular() // reflection (specular)
float kD = 1.0 - kS            // refraction (diffuse)
```

This way, we know that the amount the incoming light reflects and refracts whilst adhering to the energy conservation principle. Given this approach, it is impossible for the refracted/diffuse and reflected/specular contribution to exceed 1.0.

