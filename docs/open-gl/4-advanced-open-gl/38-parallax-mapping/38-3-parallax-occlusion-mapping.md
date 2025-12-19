Parallax Occlusion Mapping is based on the same principles as Steep Parallax Mapping, but instead of taking the texture coordinates of the first depth layer after a collision, we linearly interpolate between the depth layer before and after the collision.

The weight of the linear interpolation is determined on how far the surface's height is from the depth layer's value of both layers. 

![[38-3-parallax-occlusion-diagram.png]]

It is largely similar but takes a linear interpolation between the two depth layers' texture coordinates surrounding the intersected point. This is an approximation, but is more accurate than Steep Parallax Occlusion.

```
// Parallax Occlusion Mapping
vec2 prevTexCoords = currentTexCoords + deltaTexCoords;
float afterDepth = currentDepthMapValue - currentLayerDepth;
float beforeDepth = texture(depthMap, prevTexCoords).r - currentLayerDepth + layerDepth;
float weight = afterDepth / (afterDepth - beforeDepth);

// Return the value between the two depth maps
vec2 occludedTexCoords = prevTexCoords * weight + currentTexCoords * (1.0 - weight);

return occludedTexCoords;
```

Parallax Mapping is a great technique to boost the detail of a scene, but it does come with a few artifacts that need to be considered. Parallax mapping is used on floor or wall-like surfaces where it is not as easy to determine the surface's outline and the viewing angle is most often roughly perpendicular to the surface.  
