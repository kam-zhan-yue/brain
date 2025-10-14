In OpenGL, we work with `4x4` transformation matrices for several reasons, mainly because vectors are of size 4. The most simple transformation matrix is the identity matrix. This is a `NxN` matrix with only 0s except on its diagonal.

This transformation matrix leaves a vector 
$$
\left[
\begin{array}{cc}
1 & 0 & 0 & 0 \\
0 & 1 & 0 & 0 \\
0 & 0 & 1 & 0 \\
0 & 0 & 0 & 1 \\
\end{array}
\right]

\cdot

\left[
\begin{array}{cc}
1 \\
2 \\
3 \\
4 \\
\end{array}
\right]

=

\left[
\begin{array}{cc}
1 \cdot 1 \\
2 \cdot 1 \\
3 \cdot 1 \\
4 \cdot 1\\
\end{array}
\right]

=

\left[
\begin{array}{cc}
1 \\
2 \\
3 \\
4 \\
\end{array}
\right]
$$
The identity matrix is usually a starting point for generating other transformation matrices. If we dig deeper into linear algebra, they are useful for proving theorems and solving linear equations.