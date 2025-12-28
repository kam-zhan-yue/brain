Applying the occlusion factors in the lighting equation is easy; we just have to multiply the per-fragment ambient occlusion factor to the lighting's ambient component.

```
void main() {
  vec3 position = texture(positionBuffer, texCoords).rgb;
  vec3 normal = texture(normalBuffer, texCoords).rgb;
  vec3 colour = texture(albedoBuffer, texCoords).rgb;
  float ssao = texture(ssaoBuffer, texCoords).r;

  // Calculations
  vec3 viewDir = normalize(-position);
  vec3 lightDir = normalize(light.position - position);
  vec3 halfwayDir = normalize(lightDir + viewDir);
  float spec = pow(max(dot(normal, halfwayDir), 0.0), 8.0);
  float dist = length(light.position - position);

  // Lighting
  vec3 ambient = vec3(0.3 * colour * ssao);
  vec3 diffuse = max(dot(normal, lightDir), 0.0) * colour * light.colour;
  vec3 specular = light.colour * spec;
  float attenuation = 1.0 / (1.0 + light.linear * dist + light.quadratic * dist * dist);
  diffuse *= attenuation;
  specular *= attenuation;
  vec3 lighting = ambient + diffuse + specular;

  FragColor = vec4(lighting, 1.0);
}
```

The only change is the multiplication of the scene's ambient component by the now sampled ssao value. We also changed to view-space.

Screen-space ambient occlusion is a highly customisable effect that relies heavily on tweking its parameters based on the type of scene. There is no perfect combination of parameters for every type of scene. Some scenes only work with a small radius, while other scenes require a larger radius and a larger sample count for them to look realistic.

Some parameters can be tweaked, such as the kernel size, radius, bias, and/or size of the noise kernel. The final occlusion value can also be raised to a user-defined power to increase its strength.

```glsl
occlusion = 1.0 - (occlusion / KERNEL_SIZE);
FragColor = pow(occlusion, power);
```

Even though SSAO is a subtle effect that isn't too clearly noticeable, it adds a great deal of realism to properly lit scenes.