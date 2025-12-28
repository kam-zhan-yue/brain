Deferred rendering is great for rendering an enormous amount of light sources without a heavy cost on performance. Deferred rendering by itself doesn't allow for a very large amount of light sources as we'd still have to calculate each fragment's lighting component for each of the scene's light sources. What makes a large amount of light sources possible is an optimisation known as light volumes.

When we render a fragment in a lit scene, we calculate the contribution of each light source in a scene, regardless of their distance to the fragment. A large portion of these light sources will never reach the fragment, so we can discard these computations.

## Concept

The idea behind light volumes is to calculate the radius, or volume, of a light source. As most light sources use some for of attenuation, we can use that to calculate the maximum distance or radius their light is able to reach. We then only do the expensive lighting calculations if a fragment is inside one or more of these light volumes.

This can save us a considerable amount of computation as we now only calculate lighting where it is necessary.

## Calculating a Light Volume

To obtain a light's volume radius, we have to solve the attenuation equation for when its light contribution becomes 0.0. The attenuation equation is:

$$
F_{att} = \frac{1.0}{K_c + K_1 * d + K_q * d^2}
$$

The equation will never each 0.0, so we can solve it for a brightness value that is close to 0.0. but still perceived as dark. The brightness value for 5/256 is acceptable for the demo scene. When divided by 256, the default 8-bit framebuffer can only display that many intensities per component.
$$
\frac{5}{256} = \frac{I_{max}}{Attenuation}
$$
After substituting the original values, we are left with a quadratic equation. If we know the quadratic, linear, and constant terms, we can solve for the distance.

```c++
float constant = 1.0;
float linear = 0.7;
float quadratic = 1.8;
float lightMax = fmaxf(fmaxf(lightColor.r, lightColor.g), lightColor.b);
float radius = (-linear + sqrtf(linear * linear - 4 * quadratic * constant - (256.0 / 5.0) * lightMax)) / (2 * quadratic);
```

We calculate this radius for each light source of the scene and use it to only calculate lighting for that light source if a fragment is inside the light source's volume.

```
  for (int i=0; i<NUM_LIGHTS; ++i) {
    float distance = length(lights[i].position - position);
    if (distance >= lights[i].radius) continue;
```

## Practical Usage of Light Volumes

However, the fragment shader doesn't work in practice and only illustrates how we can sort of use a light's volume to reduce lighting calculations. The reality is that your GPU and GLSL are bad at optimising loops and branches. This is because shader execution on the GPU is highly parallel and most architectures have a requirement that for a large collection of threads, they need to run the exact same shader code for it to be efficient. This often means that a shader is run that executes all branches of an if statement to ensure the shader runs are the same for that group of threads, making our previous radius check completely useless.

The appropriate approach to using light volumes is to render actual sphered, scaled by the light volume radius, The centres of these spheres are positioned at the light source's position, and as it is scaled by the light volume radius, the sphere exactly encompasses the light's visible volume.

We then use the deferred lighting shader for rendering the spheres. As a rendered sphere produces fragment shader invocations that exactly match the pixels the light source affects, we only render the relevant pixels and skip all other pixels.

![[41-4-light-volume-pass.png]]

This is done for each light source in the scene, and the resulting fragments are additively blended together. The result is the same as before, but this time rendering only the relevant fragments per light source. 

This effectively reduces the number of computations from `nr_objects * nr_lights` to `nr_objects + nr_lights`.

There is still one issue with this approach, face culling should be enabled (otherwise we'd render a light's effect twice) and when it is enabled, the user may enter a light source's volume after which the volume isn't rendered anymore (due to back-face culling), removing the light source's influence. This can be solved by only rendering the spheres' back faces.

Rendering light volumes does take its toll on performance, and while it is generally much faster than normal deferred shading for rendering a large number of lights, there's still more that we can optimise.