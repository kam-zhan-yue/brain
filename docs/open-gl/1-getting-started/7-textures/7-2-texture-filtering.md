Texture coordinates do not depend on resolution, but can be any floating point value. Thus, OpenGL has to figure out which texture pixel (also known as a texel) to map the texture coordinate to. This is important if you have a very large object and a low resolution texture.

OpenGL has options for texture filtering:
- `GL_NEAREST` (also known as nearest neighbour or point filtering) is the default texture filtering method of OpenGL. When active, OpenGL selects the texel that centre is closest to the texture coordinate. 

![[7-2-nearest.png]]

- `GL_LINEAR` (also known as bi-linear filtering) takes an interpolated value from the texture coordinate's neighbouring texels, approximating a colour between the texels. The smaller the distance from the texture coordinate to a texel's center, the more that texels colour contributes to the sampled colour. This becomes a mixed colour of the neighbouring pixels.

![[7-2-linear.png]]

![[7-2-result.png]]

`GL_NEAREST` results in blocked patterns where we can clearly see the pixels that form the texture while `GL_LINEAR` produces a smoother pattern where the individual pixels are less visible. `GL_LINEAR` produces a more realistic output, but some developers prefer a more 8-bit look with the `GL_NEAREST` filter.

Texture filtering can be set for magnifying and minifying operations so you could use nearest neighbour filtering when textures are scaled downwards, and linear filtering for upscaled textures.

```c++
glTexParameter(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
glTexParameter(GL_TEXTURE_2D, GL_TEXTURE_MAX_FILTER, GL_LINEAR);
```

### Mipmaps

If there are thousands of objects, each with an attached texture, there will be objects far away that have the same high resolution texture attached to the objects close to the viewer. Since the objects are far away and probably produce only a few fragments, OpenGL has difficulties in retrieving the right colour value for its fragment that spans a large part of the texture.

This produces visible artifacts for small objects and waste of memory bandwidth using high resolution textures on small objects.

To solve this issue, OpenGL uses mipmaps, collections of texture images where each subsequent texture is twice as small compared to the previous one.
- After a certain distance threshold from the viewer, OpenGL will use a different mipmap texture that best suits the distance to the object
- Because the object is far away, the smaller resolution will not be noticeable to the user
- OpenGL is then able to sample the correct texels and there's less cache memory involved when sampling that part of the mipmaps.

Creating a collection of mipmapped textures for each texture image is cumbersome, but OpenGL provides a single call `glGenerateMipmaps` after we created a texture.

It is possible to filter between mipmap levels using `NEAREST` and `LINEAR` filtering for switching between mipmap levels. To specify the filtering method between mipmap levels, we can replace the original filtering methods with:
- `GL_NEAREST_MIPMAP_NEAREST`: takes the nearest mipmap to match the pixel size and uses nearest neighbour interpolation for texture sampling
- `GL_LINEAR_MIPMAP_NEAREST`: takes the nearest mipmap level and samples that level using linear interpolation
- `GL_NEAREST_MIPMAP_LINEAER`: linearly interpolates between the two mipmaps and samples the interpolated level via nearest neighbour
- `GL_LINEAR_MIPMAP_LINEAR`: linearly interpolates between the two mipmaps and samples the interpolated level via linear interpolation

We can set the filtering method using `glTexParameteri`
```c++
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
```

A common mistake is to set one of the mipmap filtering options as the magnification filter. This doesn't have any effect since mipmaps are primarily used for when textures get downscaled: texture magnification doesn't use mipmaps and giving it a mipmap filtering option will throw an error.
