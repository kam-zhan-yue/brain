There are many visual defects that we need to fix from our original shadow implementation.

### Shadow Acne
There is a stripe-pattern of black lines in an alternating fashion. This is called shadow acne.

![[35-4-shadow-acne.png]]

Since the shadow map is limited by resolution, multiple fragments can sample the same value from the depth map when they are relatively far from the light source. Several fragments sample the same depth value, hence resulting in this effect.

This is generally okay, but becomes an issue when the light source looks at an angle towards the surface since the depth map is also rendered from an angle. Several fragments then access the same tilted depth texel while some are above and some are below the floor, creating a shadow discrepancy. Because of this, some fragments are considered to be in shadow, and some are not, giving the striped pattern from the image.

We can solve this issue by using a **shadow bias** where we simply offset the depth of the surface by a small bias amount so that the fragments are not incorrectly considered below the surface.

![[35-4-shadow-bias.png]]

With this applied, all the samples get a depth smaller than the surface depeth and thus the entire surface is correctly lit without any shadows.

```glsl
float bias = 0.005;
float shadow = currentDepth - bias > closestDepth ? 1.0 : 0.0;
```

A shadow bias of `0.005` solves the issue by a large extent, but the bias value is highly dependent on the angle between the light source and the surface. If the surface would have a steep angle to the light source, the shadows may still display shadow acne. A more solid approach would be to change the amount of bias based on the surface angle towards the light. This can be done with the dot product.

```glsl
float bias = max(0.05 * (1.0 - dot(normal, lightDir)), 0.005);
```

Now, there is a maximum bias of 0.05 and a minimum of 0.005  based on the surface's normal and the light direction. This way, surfaces like the floor that are perpendicular to the light source get a small bias, while surfaces like the cube's side-faces get a much larger bias.

Choosing the correct bias values will require tweaking as this will be different for each scene. But for most of the time it's simply a matter of slowly incrementing the bias until all acne is removed.

### Peter Panning
A disadvantage of using a shadow bias is that you apply an offset to the actual depth of the objects. As a result, the bias may be large enough so that you see a visible offset of the shadows compared to the actual object location. 

![[35-4-peter-panning.png]]

This shadow artifact is known as peter panning since objects seem slightly detatched from their shadows. We can use a trick to solve most of the peter panning issues using front face culling when rendering the depth map.

By default, OpenGL cuts the back-faces. By telling OpenGL we want to cull front faces, we swap this order around. Because we only need depth values, it shouldn't matter for solid objects whether we take the depth of their front faces or their back faces. Using their back faces doesn't give the wrong result if we have shadows inside objects. This allows us to make the perceived depth in the depth buffer even lower that if it were to be raised by the shadow bias.

Hence to fix peter panning, we cull all front faces during the shadow map generation.

```glsl
glEnable(GL_CULL_FACE);
glCullFace(GL_FRONT);
RenderScene();
glCullFace(GL_BACK);
```

This solves peter panning issues, but only for solid objects that actually have an inside with openings. However, it won't work on the floor as culling the front face complete removes the floor since it is a single plane and would be culled. If we want to keep using this hack, we want to only cull front faces of objects that need to be impacted.d

Another consideration is that objects that are close to the shadow receiver may still give incorrect results. With normal bias values, we can generally avoid peter panning.

### Oversampling
Another visual discrepancy is that regions outside the light's visible frustum are considered to be in shadow, while they're (usually) not. This happens because the projected coordiantes outside the light's frustum are higher than 1.0 and will sample the depth texture outside its default range of `[1, 0]`. Based on the texture's wrapping method, we will get incorrect depth results not based on the real depth values from the light's source.

![[35-4-oversampling.png]]

You can see that there is some sort of imaginary region of light and a large part of the area is in shadow. This area represents the size of the depth map projected onto the floor. The reason this happens is because the depth map's wrapping is set to `GL_REPEAT`.

What we'd rather have is that all coordinates outside the depth map's range have a depth of 1.0, which as a result means these coordinates will never be in shadow (as no objects have a depth larger than 1.0). We can do this by configuring a texture border colour and setting the depth map's texture wrap options to `GL_CLAMP_TO_BORDER`.

```c++
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_BORDER);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_BORDER);
float borderColor[] = { 1.0, 1.0, 1.0, 1.0 };
glTexParameterfv(GL_TEXTURE_2D, GL_TEXTURE_BORDER_COLOR, borderColor);
```

Now, whenever we sample the outside the depth map's coordinate range, the texture function will always produce a depth of 1.0 due to the border colour.

However, there is still one part showing a dark region. Those are the coordinates outside the far plane of the light's orthographic frustum. This dark region always occurs at the far end of the light source's frustum by looking at the shadow directions.

A light-spaced projected fragment coordinate is further than the light's far plane when its `z` coordinate is larger than 1.0. In that case the `GL_CLAMP_TO_BORDER` wrapping method doesn't work anymore as we compare the coordinate's z component with the depth map values; this always returns true for `z` larger than 1.0

The fix is relatively easy as we simply force the `shadow` value to 0.0 whenever the projected vector's z coordinate is larger than 1.0.

```glsl
float shadowCalculation() {
	if (projCoords.z > 1.0)
		return 0.0;
}
```

The result of all this means we only have shadows where the projected fragment coordinates sit inside the depth map range, so anything outside of the light frustum will have no visible shadows. As games usually make sure this only occurs int he distance, it is a much more plausible effect than the obvious black regions we had before.