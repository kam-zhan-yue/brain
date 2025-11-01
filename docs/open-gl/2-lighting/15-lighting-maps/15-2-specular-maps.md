The specular highlights looks a bit odd since the object is a container that mostly consists of wood and wood doesn't have specular highlights like that. We can fix this by setting the specular material of the object to `vec3(0.0)`, but it would mean that the steel frame wouldn't show specular highlights. 

We can also use a texture map for the specular highlights. We need to generate a black and white texture that defines the specular intensities of each part of the object.

![[15-2-specular-map.png]]

Each pixel of the specular map can be displayed as a colour vector where black represents the colour vector `vec(0.0)` and gray the vector `vec3(0.5)`. Wood has no specular highlights, so the entire wooden section of the diffuse texture is black. The steel border has varying specular intensities with the steel itself being susceptible to specular highlights, while the cracks are not.

Tools such as *Photoshop* or *Gimp* make it relatively easy to transform a diffuse texture to a specular image by cutting out parts, transforming it to black and white, and increasing the brightness/contrast.

