## Dot Product

The dot product of two vectors is equal to the scalar product of their lengths times the cosine of the angle between them.

$$
\vec{v} \cdot \vec{k} = \|\vec{v}\|\cdot\|\vec{k}\|\cdot cos\space\theta
$$
If v and k are unit vectors, then their length would equal one, reducing the formula to:
$$
\hat{v}\cdot\hat{k} = 1\cdot1\cdot cos \space \theta= cos \space \theta
$$
The dot product only defines the angle between both vectors. This allows us to easily test if two vectors are orthogonal or parallel to each other.

## Cross Product
The cross product is only defined in 3D space and takes two non-parallel vectors as input and produces a third vector that is orthogonal to both the input vectors. If both the input vectors are orthogonal to each other as well, the cross product would result in 3 orthogonal vectors.

$$
% determinant form
\begin{align*}
\vec{a} \times \vec{b} =\begin{vmatrix}
i & j & k\\
a_1 & a_2 & a_3\\
b_1 & b_2 & b_3
\end{vmatrix} = (a_2b_3-a_3b_2)i + (a_3b_1-a_1b_3)j+(a_1b_2-a_2b_1)k
\end{align*}
$$

### Matrix-Vector Multiplication

Vectors can represent positions, colours, and texture coordinates. A vector is basically a Nx1 matrix where N is the vector's number of components. If we have an `MxN` matrix, we can multiply this matrix with our `Nx1` vector, since the columns of the matrix are equal to the number of rows of the vector.

There are many interesting 2D/3D transformations we can place inside a matrix, and multiplying that matrixs with a vector then *transforms* the vector.

