It is possible to directly pass a multisampled texture image to a fragment shader first instead of first resolving it. GLSL gives us the option to sample the texture per subsample so that we can create our own custom anti-aliasing algorithms.

To get a texture value per subsample, you'd have to define the texture uniform sampler as a `sampler2DMS` instead of the usual `sampler2D`.

```glsl
uniform sampler2DMS screenTextureMS;
```

Using the `texelFetch` function, it is then possible to retrieve the colour value per sample.

```glsl
vec4 colorSample = texelFetch(screenTextureMS, TexCoords, 3);
```
