The bidirectional reflective distribution function (BRDF) is a function that takes as input:
- the incoming (light) direction `wi`
- the outgoing (view) direction `wo`
- the surface normal `n`
- and a surface parameter `a` that represents the material's roughness

The BRDF approximates how much each individual light ray `wi` contributes to the final reflected light of an opaque surface given its material properties. For instance, if the surface has a perfectly smooth surface (like a mirror), the BRDF will return 0.0 for all incoming light rays, except the one ray that has the same reflected angle as the outgoing ray, at which the function returns 1.0

A BRDF approximates the material's reflective and refractive properties based on the previously discussed microfacet theory. For a BRDF to be physically plausible, it has to respect the law of energy conservation. Technically, Blinn-Phong is considered a BRDF taking the same `wi` and `wo` as inputs. However, it does not adhere to the law of energy conservation.  

All real-time PBR render pipelines use a BRDF known as the Cook-Torrance BRDF.

## Cook-Torrance BRDF

The function contains both a diffuse and specular part.
$$
f_{r} = k_{d}f_{lambert} + k_{s}f_{cook-torrance}
$$
- kd is the incoming light energy that gets refracted
- ks is the incoming light energy that gets reflected
- The left side of the BRDF states the diffuse part of the equation denoted as f lambert. This is known as Lambertian diffuse. It is a constant factor denoted as:
$$
f_{lambert} = \frac{c}{\pi}
$$
- c is the albedo or surface colour
- The divide by `pi` is to normalise the diffuse light as the earlier denotes integral that contains the BRDF is scaled by pi.

> The Lambertian diffuse is similar to our previous diffuse lighting, which calculated the surface colour multiplied by the dot product between the light direction and the surface normal. The dot product is still there, but moved out of the BRDF as `n * wi`

There exist different equations for the diffuse part of the BRDF which tend to look more realistic, but they are also more computationally expensive. As concluded by Epic Games,. the Lambertian diffuse is sufficient enough for most real-time rendering purposes.

The specular part of the BRDF is a bit more advanced:
$$
f_{cook-torrance} = \frac{DFG}{4(w_{o} \cdot n)(w_{i} \cdot n)}
$$
The Cook-Torrance specular BRDF is composed of three functions and a normalisation factor in the denominator. Each of the D, F, and G symbols represent a type of function that approximates a specific part of the surface's reflective properties. These are defined as the normal Distribution function, the Fresnel equation, and the Geometry function.
- Normal distribution function: approximates the amount of the surface's microfacets that are aligned to the halfway vector, influenced by the roughness of the surface. This is the primary function approximating the microfacets
- Geometry function: describes the self-shadowing property of the microfacets. When a surface is relatively rough, the surface's microfacets can overshadow other microfacets, reducing the light the surface reflects
- Fresnel equation: describes the ratio of surface equation at different surface angles.

Each of these functions are an approximation of their physics equivalents and there are more than one version of each that aims to approximate in different ways (more realistic vs. more efficient. We will be using the following:
- Trowbridge-Reitz GGX for D
- Fresnel-Schlick for F
- Smith's Schlick for G

## Normal Distribution Function
The normal distribution function D statistically approximates the relative surface area of microfacets exactly aligned to the halfway vector h. The Trowbridge-Reitz GGX function is:
$$
NDF_{GGX TR}(n, h, a) = \frac{a^2}{\pi ((n \cdot h)^2(a^2-1)+1)^2}
$$
- h is the halfway vector against the surface's microfacets
- a is the measure of the materials roughness

When roughness is low, a highly concentrated number of microfacets are aligned to the halfway vectors over a small radius. Due to this high concentration, the NDF displays a very bright spot. On a rough surface, where the microfacets are aligned in much more random directions, there are a larger number of halfway vectors h somewhat aligned to the microfacets, giving us more grayish results.

In GLSL, the equation translates to:
```c++
float ndf(vec3 N, vec3 H, float a) {
	float a2 = a * a;
	float NdotH = dot(N, H);
	float NdotH2 = NdotH * NdotH;
	float nominator = a2;
	float denominator = PI * pow(NdotH2 * (a2 - 1.0) + 1.0, 2);
	return nominator / denominator;
}
```

## Geometry Function
The geometry function statistically approximates the relative surface area where its micro surface-details overshadow each other, causing light rays to be occluded.

![[43-4-geometry.png]]

The geometry function takes a material's roughness parameter as input with rougher surfaces having a higher probability of overshadowing microfacets. The Schlick-GGX geometry function is:
$$
G_{SchlickGGX}(n, v, k) = \frac{n \cdot v}{(n \cdot v)(1-k) + k}
$$
- k is a remapping of a based on whether we're using the geometry lighting for either direct lighting or IBL lighting.
$$ k_{direct} = \frac{(a+1)^2}{8} $$
$$ k_{IBL} = \frac{a^2}{2} $$
The value of `a` differs based on how your engine translates roughness to a

### Smith's Method
To effectively approximate the geometry, we need to take into account the view direction (geometry obstruction) and the light direction (geometry shadowing). This can be done using Smith's method:
$$ G(n, v, l, k) = G_{sub}(n, v, k)G_{sub}(n, l, k) $$
This gives the following visual appearance over varying roughness R:

![[43-4-smith.png]]

The geometry function is a multiplier between 0.0 and 1.0 with 1.0 (white) measuring no shadowing, and 0.0 meaning complete shadowing.

In GLSL, it translates to:

```c++
float geometry(float NdotV, float k) {
	float nominator = NdotV;
	float denominator = NdotV * (1 - k) + k;
	return nominator / denominator;
}

float smith(vec3 N, vec3 V, vec3 L, float k) {
	float NdotV = max(dot(N, V), 0.0);
	float NdotL = max(dot(N, L), 0.0);
	float geometry1 = geometry(NdotV, k);
	float geometry2 = geometry(NdotL, k);
	return geometry1 * geometry2;
}
```

## Fresnel Equation
The Fresnel (Freh-nel) equation describes the ratio of light that gets reflected over the light that gets refracted, which varies over the angle we're looking at a surface. The moment light hits a surface, based on the surface-to-view angle, the Fresnel equation tells us the percentage of light that gets reflected. From this ratio of reflection and the energy conservation principle, we can obtain the refracted portion of light.

Every surface or material has a level of base reflectivity when looking straight at its surface. But when looking at the surface form an angle, all reflection become more apparent compared to the surface's base reflectivity. You can check this by looking at a wooden/metallic desk which has a base reflectivity form a perpendicular view angle, but looking at your desk from a 90 degree angle will show that the reflections become more apparent.

This phenomenon is known as Fresnel and is described by the Fresnel equation. The Fresnel equation can be approximated by the Fresnel-Schlick approximation:
$$
F_{Schlick}(h, v, F_{0}) = F_{0} + (1-F_{0})(1-(h \cdot v))^5
$$
- F0 represents the base reflectivity of the surface, which we calculate using the indices of refraction, or IOR
- The more we look towards the surface's grazing angles (with the halfway-view reaching 90 degrees), the stronger the Fresnel effect

![[43-4-fresnel.png]]

There are a few subtleties with the Fresnel equation.
- The Fresnel-Schlick approximation is only for dielectric (non-metal) surfaces
- For conductor (metal) surfaces, calculating the base reflectivity with IOR doesn't really hold and we need to use a different Fresnel equation altogether
- We further approximate by pre-computing the surface's response at normal incidence (F0) at a 0 degree angle as if looking directly onto a surface.
- We interpolate this value based on the view angle, such that we can use the same equation for both metals and non-metals

### Base Reflectivity
The surface's response at normal incidence, or base reflectivity, can be found in large databases.

![[43-4-base-reflectivity.png]]

For all dielectric surfaces, the base reflectivity never gets above 0.17, which is the exception rather than the rule, while for conductors the base reflectivity starts much higher and mostly varies between 0.5 and 1.0. Furthermore, for conductors, the base reflectivity is tinted. This is why F0 is represented as an RGB triplet (reflectivity at normal incidence can vary per wavelength). This is only for metallic surfaces.

These specific attributes of metallic surfaces compared to non-metallic surfaces created the metallic workflow. In the metallic workflow, we author surface materials with an extra parameter known as metalness that describes whether a surface is either metallic or non-metallic.

> Theoretically, metalness is binary. However, most render pipelines allow configuring the metalness of a surface linearly between 0.0 and 1.0. This is because of the lack of metal texture precision. For instance, a surface having small (non-metal) dust/sand-like particles over a metallic surface is difficult to render with binary metalness values.

By pre-computing F0 for both dielectrics and conductors, we can use the same Fresnel-Schlick approximation for both types of surfaces, but we do have to tint the base reflectivity if we have a metallic surface. This is as follows:

```c++
vec3 F0 = vec3(0.04);
F0 = mix(F0, surfaceColour.rgb, metalness);
```

- Define a base reflectivity that is approximated for most dielectric surfaces
- A base reflectivity of 0.04 holds for mo˜t dielectrics and produces plausible results without having to author an additional surface parameter. 
- Then, based on how metallic a surface is, we either take the dielectric base reflectivity, or take F0 as authored by the surface colour
- Because metallic surfaces absorb all refracted light, they have no diffuse reflections and we can use the surface colour texture as their base reflectivity

The Fresnel-Schlick approximation translates to:

```c++
vec3 fresnelSchlick(float cosTheta, vec3 F0) {
	return F0 + (1.0 - F0) * pow(1.0 - cosTheta, 5.0);
}
```
- `cosTheta` is the dot product between the surface's normal and the halfway vector

## Cook-Torrance Reflectance Equation
With every component of the Cook-Torrance BRDF described, we can now show the final reflectance equation as:

![[43-4-final-equation.png]]

We removed `ks` because the Fresnel term F represents the ratio of light that gets reflected on the surface (which is equivalent). This means that the specular (DFG) part of the reflectance equation implicitly contains the reflectance ratio `ks`.

This equation now completely describes a physically based render model that is generally recognised as physically based rendering (PBR).
