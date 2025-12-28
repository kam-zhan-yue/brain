Since monitors display colours with gamma applied, when we draw, edit, or paint a picture, we pick colours based on what we see on the monitor. This means that pictures we create/edit are not in linear space, but in sRGB space.

When texture artists create art by eye, all textures' values are in sRGB space so if we use those textures as they are, we have to take this into account.

Before we did gamma correction, this was not an issue because the textures looked good in sRGB space. But now that we're displaying everything in linear space, the texture colours will be off.

![[34-2-textures.png]]

The texture image becomes too bright (essentially it is double gamma corrected). To fix this issue, we have to make sure that texture artists work in linear space. However, it's easier to work in sRGB and most tools don't support linear texturing.

The solution is to re-correct or transform these sRGB textures to linear space before doing any calculations on their colour values.

```glsl
float gamma = 2.2;
vec3 diffuseColor = pow(texture(diffuse, texCoords).rgb, vec3(gamma));
```

Doing this for each texture in sRGB space is troublesome. OpenGL gives us another solution with the `GL_SRGB` and `GL_SRGBL_ALPHA` internal texture formats.

If we create a texture in OpenGL with any of these two sRGB texture formats, OpenGL will automatically correct the colours to linear space when we use them, allowing us to work in linear space.

```c++
glTexImage2D(GL_TEXTURE_2D, 0, GL_SRGB, width, height, 0, GL_RGB, GL_UNSIGNED_BYTE, image);
```

If we want to include alpha components, we need to specify it as `GL_SRGB_ALPHA`.

> Not all textures are actually in sRGB space. Textures used for colouring objects (e.g. diffuse textures) are almost always in sRGB space, but textures for retrieving lighting parameters (e.g. specular maps and normal maps) are almost always in linear space. 

