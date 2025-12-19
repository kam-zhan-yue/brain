Steep Parallax Mapping is an extension on top of parallax mapping that uses the same principles, but it takes multiple samples to better pinpoint vector B. This gives much better results even with steep height changes, as the accuracy of the technique is improved by the number of samples.

## Concept

The general idea of Steep Parallax Mapping is that it divides the total depth range into multiple layers of the same height/depth. For each of these layers, we sample the depth map, shifting the texture coordinates along the direction of P until we find a sampled depth value that is less than the depth value of the current layer.

The layers are calculated by dividing the depth range. The value at which we sample the depth is then equal to the value of P divided by the number of layers.

> E.g. if we have 10 layers, then the first layer would be at 0.1. The texture coordinate is equal to multiplying P with 0.1.

![[38-2-steep-diagram-1.png]]

We continue to iterate through the number of layers until we reach a texture coordinate where the value sampled is higher than the value of the layer.

- The depthmap value at the first layer D(T1) = 1.0 is lower than the first layer's value at 0.2, so we continue
- The depthmap value at the second layer D(T2) = 0.73 is lower than the second layer's value at 0.4, so we continue
- The depthmap value at the third layer D(T3) = 0.38 is higher than the third layer's value at 0.6, so we stop.

We can then assume that the third layer is the most viable position of the displaced geometry.

### Fragment Shader

```
vec2 parallaxMapping(vec2 texCoords, vec3 viewDir) {
  // calculate the depth of each layer
  const float numLayers = 10;
  float layerDepth = 1.0 / numLayers;
  float currentLayerDepth = 0.0;

  // amount to shift the texture coordinates per layer (from P)
  // remember that P is a normalised vector, so it is just a direction
  vec2 p = viewDir.xy / viewDir.z * depthScale;
  vec2 deltaTexCoords = p / numLayers;

  // iterate through all layers
  vec2 currentTexCoords = texCoords;
  float currentDepthMapValue = texture(depthMap, currentTexCoords).r;

  while(currentLayerDepth < currentDepthMapValue) {
    // get the depth of the next layer
    currentLayerDepth += layerDepth;
    // shift the texture coordinates along P and get the depth at that coord
    currentTexCoords -= deltaTexCoords;
    currentDepthMapValue = texture(depthMap, currentTexCoords).r;
  }

  return currentTexCoords;
}
```

We set up the number of layers, calculate the depth offset of each layer, and the texture coordinate offset that we have to shift along the direction of P per layer.

We then iterate through all the layers, starting from the top, until we find a depthmap value less than the layer's depth value.
- Each loop, we check if the texture coordinate offset is below the layer depth
- The resultant offset is subtracted from the fragment's texture coordinates

## Improvements
We can improve the algorithm slightly. When looking straight onto a surface, there isn't much texture displacement going on while there is a lot of displacement when looking at a surface from an angle.

> When you look straight at the fragment, the fragment you are looking at is likely the displaced fragment already.

We can take less samples when looking straight at surface and more samples when looking at an angle.

```
const float minLayers = 8;
const float maxLayers = 32;
float numLayers = mix(maxLayers, minLayers, max(dot(vec3(0.0, 0.0, 1.0), viewDir), 0.0));
```

The dot product of the `viewDir` and the positive z direction is used to align the number of samples to `minLayers` or `maxLayers` based on the angle we're looking towards a surface. The positive z direction equals the surface's normal vector in tangent space. If we were to look at a direction parallel to the surface, we'd use a total of 32 layers.
## Issues
Because the technique is based on a finite number of samples, we get aliasing effects and the clear distinctions between layers can be easily spotted.

![[38-2-steep-parallax-issuesd.png]]

There are two popular approaches to solve this: Relief Parallax Mapping and Parallax Occlusion Mapping.
- Relief Parallax Mapping is more accurate, but is too performance heavy
- Parallax Occlusion Mapping gives almost the same results and is also more efficient