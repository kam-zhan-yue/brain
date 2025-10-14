The true power of using matrices for transformations is that we can combine multiple transformations in a single matrix thanks to matrix-matrix multiplication. 

Say we have a vector (x, y, z) and we want to scale it by 2 and then translate it by (1, 2, 3). We need a translation and a scaling matrix or our required steps. The resultant matrix would look like:

$$
Trans.Scale =
\left[
\begin{array}{cc}
1 & 0 & 0 & 1 \\
0 & 1 & 0 & 2 \\
0 & 0 & 1 & 3 \\
0 & 0 & 0 & 1 \\
\end{array}
\right]
\cdot
\left[
\begin{array}{cc}
2 & 0 & 0 & 0 \\
0 & 2 & 0 & 0 \\
0 & 0 & 2 & 0 \\
0 & 0 & 0 & 1 \\
\end{array}
\right]
=
\left[
\begin{array}{cc}
2 & 0 & 0 & 1 \\
0 & 2 & 0 & 2 \\
0 & 0 & 2 & 3 \\
0 & 0 & 0 & 1 \\
\end{array}
\right]
$$

Matrix multiplication is not commutative, which means that their order is important. When multiplying matrices, the right-most matrix is first multiplied with the vector, so you should **read from right to left**.

It is advised to first do scaling operations, then rotations, and lastly translations when combining matrices, otherwise they may negatively affect each other.

E.g. if you first do a translation, and then scale, the translation vector would also scale.

OpenGL does not have any form of matrix or vector knowledge built in, so we have to define our own mathematics classes and functions. Luckily, there is an easy-to-use and tailored-for-OpenGL mathematics library called GLM.