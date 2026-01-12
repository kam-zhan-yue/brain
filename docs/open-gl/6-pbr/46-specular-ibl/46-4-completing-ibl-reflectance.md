To get the indirect specular part of the reflectance equation, we need to stitch both parts of the split sum approximation together.

First we get the indirect specular reflections of the surface by sampling the pre-filtered environment map using the reflection vector.

### Reflections from Prefiltered Map
In the PBR Fragment Shader, we get the indirect specular reflections of the surface by sampling the pre-filtered environment map using the reflection vector. Note that we sample the appropriate mip level based on the surface roughness, giving rougher surface *blurrier* specular reflections.

```glsl
vec3 R = reflect(-V, N);
const float MAX_REFLECTION_LOD = 4.0;
vec3 prefilteredColor = textureLod(prefilterMap, R, roughness * MAX_REFLECTION_LOD).rgb;
```

In the pre-filter step, we only convoluted the environment map up to a maximum of 5 mip levels (0 to 4), which we denote as the `MAX_REFLECTION_LOD` to ensure we don't sample a mip where there is no relevant data.

### BRDF Map
Then, we sample from the BRDF lookup texture given the material's roughness and angle between the normal and view vector.

```glsl
float NdotV = max(dot(N, V), 0.0);
vec3 F = fresnelSchlickRoughness(NdotV, F0, roughness);
vec2 envBRDF = texture(brdfLUT, vev2(NdotV, roughness)).rg;
vec3 specular = prefilteredColor * (F * envBRDF.x + envBRDF.y);
```

Given the scale and bias to F0 from the BRDF lookup texture, we combine this with the left pre-filter portion of the IBL reflectance equation and re-construct the integral result as `specular`.

```glsl
// diffuse lighting
float NdotV = max(dot(N, V), 0.0);
vec3 F = fresnelSchlickRoughness(NdotV, F0, roughness);
vec3 kS = F;
vec3 kD = vec3(1.0) - kS;
vec3 irradiance = texture(irradianceMap, N).rgb;
vec3 diffuse = irradiance * albedo;

// specular lighting - prefiltered map
vec3 R = reflect(-V, N);
const float MAX_REFLECTION_LOD = 4.0;
vec3 prefilteredColor = textureLod(prefilterMap, R, roughness * MAX_REFLECTION_LOD).rgb;

// specular lighting - brdf map
vec2 envBRDF = texture(brdfLUT, vev2(NdotV, roughness)).rg;
vec3 specular = prefilteredColor * (F * envBRDF.x + envBRDF.y);

vec3 ambient = (kS * diffuse + specular) * ambientOcclusion;
```

> We don't multiply `specular` by `kS` as we already have a Fresnel multiplication in there

![[46-4-final.png]]

## What's Next?
By now, we have a clear understanding of what PBR is about and have a PBR renderer up and running. We pre-computed all the relevant PBR image-based lighting data at the start of our application, before the render loop. This was fine for educational purposes, but not too great for any practical use of PBR.

First, the pre-computation only has to be done once, not at every startup.
Second, the moment you use multiple environment maps, you'll have to pre-compute each and every one of them at startup, which tends to build up.

For this reason, you'd generally pre-compute an environment map into an irradiance and pre-filter map just once and then store it on disk (note that the BRDF integration map isn't dependent on an environment map so you only need to calculate or load it once). This does mean you'll need to come up with a custom image format to store HDR cubemaps, including their mip levels. Or, you'll store (and load) it as one of the available formats.

Furthermore, we've described the total process in these tutorials, just as generating the pre-computed IBL images to help further understanding of the PBR pipeline. However, you'll be just as fine as using tools like IBLBaker to generate these pre-computed maps.

### Reflection Probes
These tutorials also skipped over pre-computed cubemaps as reflection probes: cubemap interpolation and parallax correction. This is the process of placing several reflection probes in your scene that take a cubemap snapshot of the scene at that specific location, which we can convolute as IBL data for that part of the scene.

By interpolating between several of these probes based on the camera's vicinity, we can achieve local high-detail image-based lighting that is simply limited by the amount of reflection probes we're wiling to place. This way, the image-based lighting could correctly update when moving from a bright outdoor section to a darker indoor section.