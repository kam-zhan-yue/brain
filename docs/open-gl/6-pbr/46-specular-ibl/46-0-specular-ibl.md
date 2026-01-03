Previously, we setup PBR with image based lighting by pre-computing an irradiance map as the lighting's indirect diffuse portion. Here, we will focus on the specular part of the reflectance equation.

![[43-4-final-equation.png]]

The Cook-Torrance specular portion isn't constant over the integral and is dependent on the incoming light direction, but also the incoming view direction. Trying to solve the integral for all incoming light directions including all possible view directions is a combinatorial overload and way too expensive to calculate on a real-time basis. Epic Games proposed a solution where they were able to pre-convolute the specular part for real time purposes, given a few compromises, known as the split sum approximation.

The split sum approximation splits the specular part of the reflectance equation into two separate parts that we can individually convolute and later combine in the PBR shader for specular indirect image based lighting. Similar to how we pre-convoluted the irradiance map, the split sum approximation requires a HDR environment map as its convolution input. 

![[46-0-specular-equation.png]]

For the same performance reasons as the irradiance convolution, we can't solve the specular part of the integral in real time and expect a reasonable performance. So preferably we'd rather pre-compute this integral to get something like a specular IBL map, sample this map with the fragment's normal, and be done with it.

However,. while we were able to pre-compute the irradiance map as the integral only depended on the direction vector, the integral depends on `wi` and `wo`.

## Split Sum Approximation
Epic Games' split sum approximation solves the issue by splitting the pre-computation into 2 individual parts that we can later combine to get the resulting pre-computed result we're after. This splits the specular integral into two separate integrals.

![[46-0-split-sum-approximation.png]]

### Pre-Filtered Environment Map
The first part (when convoluted) is known as the pre-filtered environment map which is a pre-computed environment convolution map, but it takes roughness into account. For increasing roughness levels, the environment map is convoluted with more scattered sample vectors, creating blurrier results in the pre-filtered map's mipmap levels.

For instance, a pre-filtered environment map storing the pre-convoluted result of 5 different roughness values in its 5 mipmap levels looks as follows:

![[46-0-mipmaps.png]]

We generate the sample vectors and their scattering amount using the normal distribution function (NDF) of the Cook-Torrance BRDF that takes as input a normal and view direction. As we don't know beforehand the view direction when convoluting the environment map, Epic Games makes a further approximation by assuming the view direction (and thus the specular reflection direction) to be equal to the output sample direction.

```c++
vec3 N = normalize(w_o);
vec3 R = N;
vec3 V = R;
```

This way, the pre-filtered environment convolution doesn't need to be aware of the view direction. This does mean that we don't get nice grazing specular reflection when looking at specular surface reflections, but this is an acceptable compromise.

## BRDF

The second part of the split sum equation equals the BRDF part of the specular integral. If we pretend the incoming radiance is completely white for every direction (L(p,x) = 1.0, we can pre-calculate the BRDF's response given an input roughness and an input angle between the normal *n* and the light direction *wi*. 

Epic Games stores the pre-computed BRDF's response to each normal and light direction combination on varying roughness values in a 2D lookup texture (LUT) known as the BRDF integration map. The 2D lookup texture outputs a scale (red) and a bias (green) value to the surface's Fresnel response, giving us the second part of the split specular integral

![[46-0-lookup-texture.png]]

We generate the lookup texture by treating the horizontal texture coordinate (ranged between 0.0 and 1.0) of a plane as the BRDF's input (`n * wi`) and the vertical texture coordinate as the input roughness value. With this BRDF integration map and the pre-filtered environment map, we can combine both to get the result of the specular integral.

```c++
float lod = getMipLevelFromRoughness(roughness);
vec3 prefilteredColour = textureCubeLod(prefilteredEnvMap, refVec, lod);
vec2 envBRDF = texture2D(BRDFIntegrationMap, vec2(NdotV, roughness).xy);
vec3 indirectSpecular = prefilteredColour * (F * envBRDF.x + envBRDF.y);
```