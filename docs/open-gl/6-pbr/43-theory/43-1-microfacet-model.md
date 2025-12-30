All PBR techniques are based on the theory of microfacets. The theory describes that any surface at a microscopic scale can be described by tiny little perfectly reflective mirrors called microfacets. Depending on the roughness of a surface, the alignment of these tiny little mirrors can differ a lot.

![[43-1-surfaces.png]]

The rougher a surface is, the more chaotically aligned each microfacet will be along the surface. The effect of these tiny-like mirror alignments is, that when specifically talking about specular lighting/reflection, the incoming light rays are more likely to scatter along completely different directions on rougher surfaces, resulting in a more widespread specular reflection.

In contrast, on a smooth surface, the light rays are more likely to reflect in roughly the same direction, giving us smaller and sharper reflections.

![[43-1-reflections.png]]

## Roughness
No surface is completely smooth on the microscopic level, but seeing has these microfacets are small enough that we can't make a distinction between them on a per-pixel basis, we statistically approximate the surface's microfacet roughness given a roughness parameter.

Based on the roughness of a surface, we can calculate the ratio of microfacets roughly aligned to some vector *h*, which is the halfway vector that sits between the light and view vector.

The more the microfacets are aligned to the halfway vector, the sharper and stronger the specular reflection. Higher roughness values display a much larger specular reflection shape, in contrast with the smaller and sharper specular reflection shape of smooth surfaces.