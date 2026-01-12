## Cubemap Seams at High Roughness

Sampling the pre-filter map on surfaces with a rough surface means sampling the pre-filter map on some of its lower mip levels. When sampling cubemaps, OpenGL by default doesn't linearly interpolate across cubemap faces. Because the lower mip levels are of a lower resolution and the pre-filter map is convoluted with a larger sample love, the lack of between-cube-face filtering becomes apparent. 

![[46-2-seams.png]]

To fix this, OpenGL gives us the option to properly filter across cubemap faces by enabling `GL_TEXTURE_CUBE_MAP_SEAMLESS`

```c++
glEnable(GL_TEXTURE_CUBE_MAP_SEAMLESS);
```

## Bright dots in Pre-Filter Convolution
Due to high frequency details and wildly varying light intensities in specular reflections, convoluting the specular reflections requires a large number of samples to properly account for the wildly varying nature of HDR environmental reflections. We already take a very large number of samples, but on some environments it may still not be enough at some of the rougher mip levels.

This results in seeing dotted patterns emerge around bright areas.

![[46-2-dots.png]]

We can increase the sample count, but this won't be enough for all environments.

We can reduce this artifact by not directly sampling the environment map, but sampling a mip level of the environment map based on the integral's PDF and the roughness.

```glsl
if (NdotL > 0.0) {
  // Chetan Jag's method to reduce dot artifacts on brighter lights
  float D = DistributionGGX(N, H, roughness);
  float NdotH = max(dot(N, H), 0.0);
  float HdotV = max(dot(H, V), 0.0);
  float pdf = (D * NdotH / (4.0 * HdotV)) + 0.0001;

  float resolution = 512.0; // resolution of the source cubemap (per face)
  float saTexel = 4.0 * PI / (6.0 * resolution * resolution);
  flaot saSample = 1.0 / (float(SAMPLE_COUNT) * pdf + 0.0001);

  float mipLevel = roughness == 0.0 ? 0.0 : 0.5 * log2(saSample / saTexel);

  prefilteredColour += textureLod(environmentMap, L, mipLevel).rgb * NdotL;
  totalWeight += NdotL;
}
```

We also need to enable trilinear filtering on the environment map to sample its mip levels from.

```c++
  unsigned int environmentCubemap;
  glGenTextures(1, &environmentCubemap);
  glBindTexture(GL_TEXTURE_CUBE_MAP, environmentCubemap);
  for (unsigned int i = 0; i < 6; ++i) {
    // store each face with 16 bit floating point values
    glTexImage2D(GL_TEXTURE_CUBE_MAP_POSITIVE_X + i, 0, GL_RGB16F, 512, 512, 0, GL_RGB, GL_FLOAT, nullptr);
  }
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE);
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_R, GL_CLAMP_TO_EDGE);
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR); // enable pre-filter mipmap sampling
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
```

Then we need to let OpenGL generate the mipmaps **after** the cubemap's base texture is set

```c++
// Let OpenGL generate mipmaps from the first mip face (combatting dots artifact)
glBindTexture(GL_TEXTURE_CUBE_MAP, environmentCubemap);
glGenerateMipmap(GL_TEXTURE_CUBE_MAP);
```

This works to remove most, if not all, dots from the pre-filter map on rougher surfaces.