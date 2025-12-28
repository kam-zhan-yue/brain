Phong lighting is a very efficient approximation for lighting, but its specular reflections break down in certain conditions, specifically when the shininess property is low. This results in a large (rough) specular area.

Below shows a specular shininess of 1.0 on a flat textured plane.

![[33-1-breakdown.png]]

You can see at the edges that the specular area is immediately cut off. This happens because the angle between the view and reflection vector doesn't go over 90 degrees. If the angle is above 90, the dot product becomes negative and this results in a specular exponent of 0.0.

With the diffuse component, an angle higher than 90 degrees between the normal and light source means the light source is below the light˜ed surface and thus the light's diffuse contribution should equal 0.0. However with specular lighting, we're not measuring the angle between the light source and the normal, but between the view and reflection vector.

![[33-1-angles.png]]

The image on the right shows how the angle between the view and reflection vector can go above 180 degrees. This nullifies the specular contribution. This generally isn't a problem since the view direction is far from the reflection direction, but if we use a low specular component, the specular radius is large enough to have a contribution under these conditions. Since we nullify this contribution at angles larger than 90 degrees, we get the artifact in the first image.

The Blinn-Phong shading model was introduced as an extension to the Phong shader. It is largely similar, but approaches the specular model slightly differently which overcomes this problem. Instead of relying on a reflection vector, we use a halfway vector that is a unit vector exactly halfway between the view direction and the light direction. The closer this halfway vector aligns with the surface's normal vector, the higher the specular contribution.

![[33-1-halfway-vector.png]]

When the view direction is perfectly aligned with the reflection vector, the halfway vector aligns perfectly with the normal vector. The closer the view direction is to the original reflection direction, the stronger the specular highlight.

At whatever direction the viewer looks from, the angle between the halfway vector and the surface normal never exceeds 90 degrees (unless the light is far below the surface). The results are slightly different from Phong reflections, but generally more visually plausible, especially with low specular components.

The Blinn-Phong shading model is the exact shading model used in the earlier fixed function pipeline of OpenGL.

The halfway vector is:

$$
H = \frac{L + V}{||L + V||}
$$
```glsl
vec3 lightDir = normalize(lightPos - FragPos);
vec3 viewDir  = normalize(viewPos - FragPos);
vec3 halfwayDir = normalize(lightDir + viewDir);
```

The actual calculation of the specular term becomes a clamped dot product between the surface normal and the halfway vector to get the cosine angle between them that we again raise to a specular shininess exponent.

```glsl
float spec = pow(max(dot(normal, halfwayDir), 0.0), shininess);
vec3 specular = lightColor * spec;
```

With the introduction of the halfway vector, we don't see the specular cutoff issue of Phong shading. Another subtle difference is that the angle between the halfway vector and the surface normal is often shorter than the angle between the view and reflection vector. As a result, to get visuals similar to Phong shading, the specular shininess exponent has to be set a bit higher.

> A general rule of thumb is to set it between 2 and 4 times the Phong shininess exponent.