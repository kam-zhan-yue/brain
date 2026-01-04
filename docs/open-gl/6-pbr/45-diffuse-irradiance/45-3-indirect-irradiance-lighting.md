The irradiance map represents the diffuse part of the reflectance integral as accumulated from all surrounding indirect light. Seeing as the light doesn't come from direct light sources, but from the surrounding environment, we treat both the diffuse and specular indirect lighting as the ambient lighting, replacing our previously set constant term.

Firstly, we add the pre-calculated irradiance map as a cube sampler:

```glsl
uniform samplerCube irradianceMap;
```

Given the irradiance map that holds all of the scene's indirect diffuse light, retrieving the irradiance influencing the fragment is as simple as a single texture sample.

```glsl
// vec3 ambient = vec3(0.03);
vec3 ambient = texture(irradianceMap, N).rgb;
```

However, as the indirect lighting contains both a diffuse and specular part, we need to weigh the diffuse part accordingly. Similar to what we did in the previous chapter, we use the Fresnel equation to determine the surface's indirect reflectance ratio from which we derive the refractive (or diffuse) ratio.

```glsl
vec3 kS = fresnelSchlick(max(dot(N, V), 0.0), F0);
vec3 kD = vec3(1.0) - kS;
vec3 irradiance = texture(irradianceMap, N).rgb;
vec3 diffuse = irradiance * albedo;
vec3 ambient = (kD * diffuse) * occlusion;
```

## Recalculating Fresnel

As the ambient light comes from all directions within the hemisphere oriented around the normal, there's no single halfway vector to determine the Fresnel response. To still stimulate Fresnel, we calculate the Fresnel from the angle between the normal and the view vector. 

However, earlier we used the micro-surface halfway vector, influenced by the roughness of the surface, as input to the Fresnel equation. As we currently don't take roughness into account, the surface's reflective ratio will always end up relatively high. Indirect light follows the same properties of direct light, so we expect rougher surfaces to reflect less strongly on the surface edges.k

Because of this, the indirect Fresnel reflection strength looks off on rough non-metal surfaces.

![[45-3-indirect-fresnel.png]]

We can alleviate this issue by injecting a roughness term in the Fresnel-Schlick equation as described by Sébastien Lagarde:

```glsl
vec3 fresnelSchlickRoughness(float cosTheta, vec3 F0, float roughness) {
	return F0 + (max(vec3(1.0 - roughness), F0) - F0) * pow(1.0 - cosTheta, 5.0);
}
```

By taking account of the surface's roughness, the ambient code is:

```glsl
vec3 kS = fresnelSchlickRoughness(max(dot(N, V), 0.0), F0, roughness);
vec3 kD = vec3(1.0) - kS;
vec3 irradiance = texture(irradianceMap, N).rgb;
vec3 diffuse = irradiance * albedo;
vec3 ambient = (kD * diffuse) * ambientOcclusion;
```

The actual image based lighting computation is simple and only requires a single cubemap texture lookup; most of the work is in pre-computing or convoluting the irradiance map.

![[45-3-final.png]]

It still looks a bit weird as the more metallic spheres require some form of reflection to properly start looking like metallic surfaces (as metallic surfaces don't reflect diffuse lights). However, the surface response now reacts accordingly to the environment's ambient lighting.