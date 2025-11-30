The produce to make omnidirectional shadow maps is similar to directional shadow mapping, although this time we bind a cubemap texture instead of a 2D texture and also pass the light projection's far plane to the shaders.

Too lazy to write all here. Best documented in code.

However, the idea is that we generate the depth cubemap, then pass that as a texture. We sample the position of the fragment in relation to the light to get the direction, which we can pass into the texture to get the depth. We store this as the distance from the light to the object on the depth map so we can compare the frag to light position against this.

## Visualising the Cubemap Depth Buffer

We can try to debug the depth buffer by displaying the `closestDepth` variable.

```glsl
FragColor = vec4(vec3(closestDepth / farPlane), 1.0);
```

