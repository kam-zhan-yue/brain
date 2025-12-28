When tangent vectors are calculated on larger meshes that share a considerable amount of vertices, the tangent vectors are generally averaged to give nice and smooth results. A problem with this approach is that the three TBN vectors could end up non-perpendicular, which means the resulting matrix would no longer be orthogonal. Normal mapping would only be slightly off with a non-orthogonal TBN, but it is something that we can improve.

The Gram-Schmidt process allows us to re-orthogonalize the TBN vectors such that each vector is again perpendicular to the other vectors. 

This generally improves the normal mapping results with little extra cost.

```
// Gram-Schmidt method
mat3 normalMatrix = transpose(inverse(mat3(model)));
vec3 T = normalize(normalMatrix * aTangent);
vec3 N = normalize(normalMatrix * aNormal);
// re-orthogonalise T with respect to N
T = normalize(T - dot(T, N) * N);
// retrieve B with the cross product of T and N
vec3 B = cross(N, T);
```