When we're scaling a vector, we are increasing the length of the arrow we'd like to scale, while keeping the direction the same. Since we're working in either 2 or 3 dimensions, we can define scaling a vector by 2 or 3 scaling variables, each scaling one axis (x, y, or z).

Let's say we have a vector `v = (3, 2)` that we want to scale:
- Along the x-axis by 0.5
- Along the y-axis by 2
The resultant 2D vector is `s = (1.5, 4)`

We can build a transformation matrix that does the scaling for us. We can use different points on the identity matrix to scale different components of the vector.

$$
\left[
\begin{array}{cc}
S_1 & 0 & 0 & 0 \\
0 & S_2 & 0 & 0 \\
0 & 0 & S_3 & 0 \\
0 & 0 & 0 & 1 \\
\end{array}
\right]

\cdot

\left(
\begin{array}{cc}
x \\
y \\
z \\
1 \\
\end{array}
\right)

=

\left(
\begin{array}{cc}
S_1 \cdot x \\
S_2 \cdot y \\
S_3 \cdot z \\
1 \\
\end{array}
\right)
$$

Note that we keep the 4th scaling value as 1. The `w` component is used for other purposes.