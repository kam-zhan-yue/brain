Shadows are the result of the absence of light due to occlusion. When a light source's light rays do not hit an object because it is occluded by some other object, the object is in shadow. Shadows add a great deal of realism to a lit scene. However, it is tricky to implement because in current real-time (rasterised graphics) research, a perfect shadow algorithm has not been developed yet.

One technique that gives decent results is known as shadow mapping.

## Shadow Mapping
The concept behind shadow mapping is to render the scene from the light's point of view and everything we see from the light's perspective is lit and everything we can't see is in shadow.

![[35-1-light-ray.png]]

The blue lines represent the fragments the light source can see and the black lines show the occluded fragments. We want to get the point on the ray where it first hit an object and compare this to the closest point to other points int he ray. We then do a basic test to see if a test point's ray position is further down the ray than the closest point. If so, then the test point is in shadow.

Iterating through possibly thousands of light rays is inefficient. Instead of casting light rays, we can use the depth buffer.

A value in the depth buffer corresponds to the depth of the fragment clamped to [0, 1] from the camera's point of view. We can do the same from a light source's perspective and store the resulting depth values in a texture. This way, we can sample the closest depth values as seen from the light's perspective. The depth values show the first fragment visible from the light's perspective. The resultant texture is known as a depth map, or shadow map.

![[35-1-depth-map.png]]

The left image shows a directional light source casting a shadow on the surface below the cube. Using the depth values stored in the depth map, we find the closest point and use that to determine whether fragments are in shadow. We create the depth map by rendering the scene from the light's perspective using a view and projection matrix specific to that light source. The projection and view matrix form a transformation that transforms any 3D position to the light's coordinate space.

> A directional light doesn't have a position since it is theoretically is infinitely far away. For the sake of shadow mapping, we render the scene from a position somewhere along the lines of the light direction.

The right image shows the directional light and the viewer. We render a fragment at point P for which we have to determine whether it is in shadow. To do this, we first transform point P to the light's coordinate space using T. Since point P is now as seen from the light's perspective, the z coordinate corresponds to its depth (0.9). Using point P we also index the depth/shadow map to obtain the closest visible depth, which at point C. Since indexing the depth map returns a depth smaller than the depth at point P, we can conclude that point P is occluded and thus in shadow.

Shadow mapping consists of two passes:
- The first pass renders the shadow map
- The second pass renders the scene and uses the shadow map to calculate whether fragments are in shadow