Translation is the process of adding another vector on top of the original vector to return a new vector with a different position, thus *moving* the vector based on a translation vector.

Just like the scaling matrix, there are several locations on a 4x4 matrix that we can use to perform certain operations. If we represent the translation vector as:
$$
(T_x, T_y, T_z)
$$
We can define the translation matrix by:

$$
\left[
\begin{array}{cc}
1 & 0 & 0 & T_x \\
0 & 1 & 0 & T_y \\
0 & 0 & 1 & T_z \\
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
x + T_x\\
y + T_y \\
z + T_z \\
1 \\
\end{array}
\right)
$$

This works because all of the translation values are multiplied by the vector's `w` column and added to the vector's original values. This wouldn't have been possible with a 3x3 matrix.

### Homogenous Coordinates
The `w` component of a vector is also known as a homogenous coordinate. To get the 3D vector from a homogenous vector, we divide the x, y, and z coordinate by its `w` coordinate. We usually do not notice this since the `w` coordinate is 1.0 most of the time. This allows us to do matrix translations on 3D vectors.

With a translation matrix, we can move objects in any of the 3 axis directions.