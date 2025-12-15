Normal vectors in a normal map are expressed in tangent space where normals always point roughly in the positive z direction. Tangent space is a space that's local to the surface of a triangle: the normals are relative to the local reference frame of the individual triangles. It is like the local space of the normal map's vectors; they're all defined pointing in the positive z direction regardless of the final transformed direction. Using a specified matrix, we can then transform normal vectors from this *local* tangent space to world or view coordinates, orienting them along the final mapped surface's direction.

Let's say we have the incorrect normal mapped surface from the previous section looking in the positive y direction. The normal map is defined in tangent space, so one way to solve the problem is to calculate a matrix to transform normals from tangent space to a different space such that they're aligned with the surface's normal direction: the normal vectors are then all pointing roughly in the positive y direction. The great thing about tangent space is that we can calculate this matrix for any type of surface so that we can properly align the tangent space's z direction to the surface's normal direction.

Such a matrix is called a TBN matrix where the letters depict a Tangent, Bitangent, and Normal vector. These are the vectors required to construct this matrix. To construct a *change of basis* matrix. that transforms a tangent-space vector to a different coordinate space, we need three perpendicular vectors that are aligned along the surface of a normal map: an up, right, and forward vector.

The up vector is the surface's normal vector. The right and forward vectors are the tangent and bitangent vectors respectively. 

![[37-2-tbn-matrix.png]]

Calculating the tangent and bitangent vectors is not straightforward. The direction of the normal map's tangent and bitangent vector align with the direction in which we define a surface's texture coordinates.

![[37-2-bitangent.png]]

From the image, we can see that the texture coordinate differences of the edge E2 of a triangle are expressed in the same direction as the tangent vector T and bitangent vector B. Because of this, we can write both displayed edges E1 and E2 of the triangle as a linear combination of the tangent vector and the bitangent vector.

$$
E_{1} = \triangle U_{1}T+ \triangle V_{}
$$