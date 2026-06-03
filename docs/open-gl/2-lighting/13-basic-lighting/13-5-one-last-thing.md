We passed the normal vector directly from the vertex shader to the fragment shader. However, the calculations in the fragment shader are all done in world space, so shouldn't we transform the normal vectors to world space coordinates as well?

Basically, yes, but it's not as simple as multiplying it with the model matrix.

Firstly, normal vectors are only direction vectors and do not represent a specific position in space. Normal vectors do not have a homogenous coordinate. This means that translations should not have any effect on the normal vectors. So if we want to multiply the normal vectors with a model matrix, we want to remove the translation part of the matrix by taking the upper-left 3x3 matrix of the model matrix.

Secondly, if the model matrix would perform a non-uniform scale, the vertices would be changed in such a way that the normal vector is not perpendicular to the surface anymore. The following image shows the effect a model matrix (with non-uniform scaling) would have on the normal vector.

![[13-5-one-last-thing.png]]

When we apply a non-uniform scale, the normal vectors are not perpendicular to the corresponding surface anymore, distorting the lighting.

The trick of fixing this behaviour is to use a different model matrix tailored for normal vectors. This matrix is the normal matrix and uses a few linear algebraic operations to remove the effect of wrongly scaling the normal vectors.

The normal matrix is defined as:

> The transpose of the inverse of the upper-left 3x3 part of the model matrix.

In the vertex shader, we can generate the normal matrix by using the `inverse` and `transpose` functions in the vertex shader that work on any matrix type. We can cast the matrix to a 3x3 matrix to ensure it loses its translation properties and that it can multiply with the `vec3` normal vector.

```glsl
Normal = mat3(transpose(inverse(model))) * aNormal;
```

> Inversing matrices is a costly operation for shaders, so wherever possible try to avoid doing inverse operations since they have to be done on each vertex on your scene. For an efficient application, you'd likely want to calculate the normal matrix on the CPU and send it to the shaders via a uniform before drawing (just like the model matrix).
