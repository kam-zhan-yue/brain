We currently render our meshes on flat triangles, hiding the fact that the polygons have no depth. Most real-life surfaces aren't flat and exhibit bumpy details. For instance, a brick surface is quite a rough surface and lighting doesn't take any small cracks and holes into account. We can partly fix the look by using a specular map to add depth, but we need a way to inform the lighting system about all the depth-like details of the surface.

From the lighting's point of view, the only way it determines the shape of an object is by its perpendicular normal vector. The brick surface only has a single normal vector, and as a result the surface is uniformly lit based on this normal vector's direction.

Instead of a per-surface normal that is the same for each fragment, we can use a per-fragment normal that is different for each fragment, deviating the normal vector based on the surface's little details and giving the illusion that the surface is more complex.

![[37-1-normal-representation.png]]

The technique to use per-fragment normal compared to per-surface normals is called normal mapping or bump mapping.

## Normal Mapping
To get normal mapping to work, we need a per-fragment normal. We can use a 2D texture to store per-fragment normal data to get a normal vector for a specific fragment.

While normal vectors are geometric entities and textures are generally only used for colour information, storing normal vectors in a texture way may not be immediately obvious. If you think about colour vectors in a texture they are represented as a 3D vector with an r, g, b component, we can similar store a normal vector's x, y, z component in the respective colour components. Normal vectors range between -1 and 1 so they're first mapped to [0, 1].

```
vec3 rgb_normal = normal * 0.5 + 0.5; // transforms from [-1, 1] to [0, 1]
```

With normal vectors transformed to an RGB colour component, we can store a per-fragment normal derived from the shape of a surface onto a 2D texture. An example is shown below:

![[37-1-normal-map.png]]

Almost all normal maps have a blueish tine because the normals are closely pointing outwards to the positive z-axis (0, 0, 1): a blue-ish colour. The deviations in colour represent normal vectors that are slightly offset from the general positive z direction, giving a sense of depth to the texture.

With a simple plane, looking at the positive z-axis, we can take a diffuse texture and a normal map to render the image from the previous section. The linked normal map is different from the one shown above. The reason for this is that OpenGL reads texture coordinates with the y coordinate reversed from how the textures are generally created. The linked normal map thus has its y (or green) component inversed. If you fail to take this into account, **the lighting will be incorect**.