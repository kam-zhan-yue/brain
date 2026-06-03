Diffuse lighting is based on the light's direction vector and the object's normal vectors. Specular lighting is additionally based on the view direction (from the direction the player is looking at the fragment). It is based on the reflective properties of surfaces. If we think of the object's surface as a mirror, the specular lighting is the strongest where we would see light reflected on the surface

![[13-6-specular-lighting.png]]

We calculate a reflection vector by reflecting the light direction around the normal vector. Then we calculate the angular distance between this reflection vector and the view direction. The closer the angle between them, the less the impact of specular light, but the greater the angle between them, the greater the impact of specular light. The resulting effect is that we see a bit of highlight when we're looking at the light's direction reflected via the surface.

The view vector is the extra variable we need for specular lighting which we can calculate using the viewer's world space position and the fragment's position. Then we calculate the specular's intensity, multiply this with the light colour and add this to the ambient and diffuse components.

```glsl
vec3 viewDir = normalize(viewPos - FragPos);
vec3 reflectDir = reflect(-lightDir, norm);
```

We negate the `lightDir` vector. The `reflect` function expects the first vector to point from the light source towards the fragment's position, but the `lightDir` vector is currently pointing the other way around: from the fragment towards the light source.

Then, we calculate the specular component like such:
```glsl
float spec = pow(max(dor(viewDir, reflectDir), 0.0), 32);
vec3 specular = specularStrength * spec * lightColour;
```

We calculate the dot product between the view direction and the reflect direction (and make sure it's not negative) then we raise it to the power of 32. The 32 value is the shininess value of the highlight. The higher the shininess value of an object, the more it properly reflects the light instead of scattering it all around and thus the smaller the highlight becomes. 

![[13-6-shininess.png]]

`
