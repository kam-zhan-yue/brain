Normal vectors in a normal map are expressed in tangent space where normals always point roughly in the positive z direction. Tangent space is a space that's local to the surface of a triangle: the normals are relative to the local reference frame of the individual triangles. It is like the local space of the normal map's vectors; they're all defined pointing in the positive z direction regardless of the final transformed direction. Using a specified matrix, we can then transform normal vectors from this *local* tangent space to world or view coordinates, orienting them along the final mapped surface's direction.

Let's say we have the incorrect normal mapped surface from the previous section looking in the positive y direction. The normal map is defined in tangent space, so one way to solve the problem is to calculate a matrix to transform normals from tangent space to a different space such that they're aligned with the surface's normal direction: the normal vectors are then all pointing roughly in the positive y direction. The great thing about tangent space is that we can calculate this matrix for any type of surface so that we can properly align the tangent space's z direction to the surface's normal direction.

Such a matrix is called a TBN matrix where the letters depict a Tangent, Bitangent, and Normal vector. These are the vectors required to construct this matrix. To construct a *change of basis* matrix. that transforms a tangent-space vector to a different coordinate space, we need three perpendicular vectors that are aligned along the surface of a normal map: an up, right, and forward vector.

The up vector is the surface's normal vector. The right and forward vectors are the tangent and bitangent vectors respectively. 

![[37-2-tbn-matrix.png]]

Calculating the tangent and bitangent vectors is not straightforward. The direction of the normal map's tangent and bitangent vector align with the direction in which we define a surface's texture coordinates.

![[37-2-bitangent.png]]

From the image, we can see that the texture coordinate differences of the edge E2 of a triangle are expressed in the same direction as the tangent vector T and bitangent vector B. Because of this, we can write both displayed edges E1 and E2 of the triangle as a linear combination of the tangent vector and the bitangent vector.
$$
E_{1} = \triangle U_{1}T+ \triangle V_{1}B
$$
$$
E_{2} = \triangle U_{2}T+ \triangle V_{2}B
$$
We can also write this as
$$
(E_{1x},E_{1y},E_{1z}) = \triangle U_{1}(T_{x}, T_{y}, T_{z}) + \triangle V_{1}(B_{x}, B_{y}, B_{z})
$$

$$
(E_{2x},E_{2y},E_{2z}) = \triangle U_{2}(T_{x}, T_{y}, T_{z}) + \triangle V_{2}(B_{x}, B_{y}, B_{z})
$$

We can calculate E as the difference vector between two triangle positions and their texture coordinate differences. We're then left with two unknowns and two equations. We can write this in matrix multiplication to solve for the unknowns.

![[37-2-equation.png]]

> As long as we have the triangle's vertices and texture coordinates (these are in the same space as the tangent vectors), we can calculate the tangents and bitangets.

## Manual Calculation of Tangents and Bitangents

```c++
// triangle 1
glm::vec3 edge1 = pos2 - pos1;
glm::vec3 edge2 = pos3 - pos1;
glm::vec2 deltaUV1 = uv2 - uv1;
glm::vec2 deltaUV2 = uv3 - uv1;

float f = 1.0f / (deltaUV1.x * deltaUV2.y - deltaUV2.y - deltaUV2.x);
glm::vec3 tangent1, bitangent1;
tangent1.x = f * (deltaUV2.y * edge1.x - deltaUV1.y * edge2.x);
tangent1.y = f * (deltaUV2.y * edge1.y - deltaUV1.y * edge2.y);
tangent1.z = f * (deltaUV2.y * edge1.z - deltaUV1.y * edge2.z);

bitangent1.x = f * (-deltaUV2.x * edge1.x + deltaUV1.x * edge2.x);
bitangent1.y = f * (-deltaUV2.x * edge1.y + deltaUV1.x * edge2.y);
bitangent1.z = f * (-deltaUV2.x * edge1.z + deltaUV1.y * edge2.z);


// triangle 2
edge1 = pos3 - pos1;
edge2 = pos4 - pos1;
deltaUV1 = uv3 - uv1;
deltaUV2 = uv4 - uv1;

glm::vec3 tangent2, bitangent2;
f = 1.0f / (deltaUV1.x * deltaUV2.y - deltaUV2.y - deltaUV2.x);
tangent2.x = f * (deltaUV2.y * edge1.x - deltaUV1.y * edge2.x);
tangent2.y = f * (deltaUV2.y * edge1.y - deltaUV1.y * edge2.y);
tangent2.z = f * (deltaUV2.y * edge1.z - deltaUV1.y * edge2.z);

bitangent2.x = f * (-deltaUV2.x * edge1.x + deltaUV1.x * edge2.x);
bitangent2.y = f * (-deltaUV2.x * edge1.y + deltaUV1.x * edge2.y);
```

We pre-calculate the fractional part of the equation as `f` and then for each vector component we do the corresponding matrix multiplication multiplied by `f`. Because a triangle is always a flat shape, we only need to calculate a single tangent/bitangent pair per triangle as they will be the same for each of the triangle's vertices. 

The resultant tangent and bitangent vectors should have a value of (1,0,0) and (0,1,0) respectively that together with the normal (0,0,1), forms an orthogonal TBN matrix.

### Tangent Space Normal Wrapping

To get normal mapping working, we create a TBN matrix in the shaders.

```glsl
vec3 T = normalize(vec3(model * vec4(aTangent, 0.0)));
vec3 B = normalize(vec3(model * vec4(aBitangent, 0.0)));
vec3 N = normalize(vec3(model * vec4(aNormal, 0.0)));
mat3 TBN = mat3(T, B, N);
```

We first transform all the TBN vectors to the coordinate system we'd like to work in, which in this case is the world- space as we multiply them with the model matrix. Then we create the actual TBN matrix by directly supplying the constructor with the relevant vectors. If we wanted to be really precise. we would multiply the TBN vectors with the normal matrix as we only care about the orientation of the vectors.

> There is also no technical need for the bitangent variable. All three TBN vectors are perpendicular to each other, so we can calculate the bitangent by taking the cross product of the T and N vector.

With a TBN matrix, there are now two ways we can use it.
1. We take the TBN matrix that transforms any vector from tangent to world space, give it to the fragment shader, and transform the sampled normal from tangent space to world space using the TBN matrix; the normal is then in the same space as the other lighting variables.
2. We take the inverse of the TBN matrix that transforms any vector from world space to tangent space, and use this matrix to transform all other relevant lighting variables to tangent space; the normal is then again in the same space as the other lighting variables.

### Tangent to World Space
```
// obtain normal from the normal map in range [0, 1]
vec3 normal = texture(normalMap, f_in.texCoords).rgb;
// transform into range [-1, 1]
normal = normalize(normal * 2.0 - 1.0);
// transform from tangent space to world space
normal = normalize(f_in.TBN * normal);
```

In the above, the resulting normal is now in world space, there is no need to change any of the other fragment shader code as the lighting code assumes the normal vector to be in world space.

### World to Tangent Space
We can use the transpose function (instead of the `inverse` function) as the transpose of an orthogonal matrix equals its inverse.

```
v_out.TBN = transpose(mat3(T, B, N));
```

Then we would have to transform the other vectors to tangent space.

```
vec3 lightDir = f_in.TBN * normalize(lightPos - f_in.position);
vec3 viewDir = f_in.TBN * normalize(viewPos - f_in.position);
```

The second approach seems like more work, but transforming vectors from world space to tangent space has the added advantage that we can transform all the relevant lighting vectors to tangent space in the vertex shader instead of in the fragment shader. This works because lightPos and viewPos don't update every fragment run, and we can calculate the `f_in.position`'s tangent space position and let fragment interpolation do the work.

There is effectively no need to transform a vector to tangent space in the fragment shader, while it is necessary with the first approach as sampled normal vectors are specific to each fragment shader run.

### Optimisations
Instead of sending out the inverse of the TBN matrix to the fragment shader, we send a tangent-space light position, view position, and vertex position to the fragment shader. This saves us from having to do matrix multiplications in the fragment shader. This is a nice optimisation as the vertex shader runs considerably less often than the fragment shader. This is also the preferred approach.

```
vec3 T = normalize(vec3(model * vec4(aTangent, 0.0)));
vec3 B = normalize(vec3(model * vec4(aBitangent, 0.0)));
vec3 N = normalize(vec3(model * vec4(aNormal, 0.0)));
mat3 TBN = transpose(mat3(T, B, N));
v_out.tangentLightPos = TBN * lightPos;
v_out.tangentViewPos = TBN * viewPos;
v_out.tangentFragPos = TBN * vec3(model * vec4(aPos, 0.0));
```

We can then use these values in the fragment shader to calculate lighting in tangent space. As the normal vector is already in tangent space, the lighting makes sense.