Diffuse lighting gives the object more brightness the closer the fragments are aligned to the light rays from a source.

![[13-2-diffuse-diagram.png]]

To the left, there is a light source with a light ray targeted at a single fragment of the object. We need to measure what angle the light ray touches the fragment. If it is perpendicular to the surface, then the light has the greatest impact. We use a normal vector to measure this. The angle between the two vectors can then be calculated with the dot product.

We need the following to calculate diffuse lighting:
- Normal vector
- The directed light ray