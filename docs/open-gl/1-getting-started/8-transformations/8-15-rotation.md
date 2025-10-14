A rotation in 2D or 3D is represented with an angle. An angle could be in degrees or radians. Most rotation functions require an angle in radians.

Rotations in 3D are specified with an angle and a rotation axis. The angle specified will rotate the object along the rotation axis given. This can be imagined by spinning your head a certain degree while looking down a single rotation axis. When rotating 2D vectors in a 3D world, we set the rotation axis to the z-axis.

Using trigonometry, we can transform vectors to newly rotated vectors given an angle. A rotation matrix is defined for each unit axis in 3D space where the angle is represented as θ.

Rotation around the x-axis:
$$
\left[
\begin{array}{cc}
1 & 0 & 0 & 0 \\
0 & cos\space\theta & -sin\space\theta & 0 \\
0 & sin\space\theta & cos\space\theta & 0 \\
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
x\\
y \cdot cos\space\theta - z \cdot sin\space\theta \\
y \cdot sin\space\theta + z \cdot cost\space\theta \\
1 \\
\end{array}
\right)
$$

Rotation around the y-axis:
$$
\left[
\begin{array}{cc}
cos\space\theta & 0 & sin\space\theta & 0 \\
0 & 1 & 0 & 0 \\
-sin\space\theta & 0 & cos\space\theta & 0 \\
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
x \cdot cos\space\theta + z \cdot sin\space\theta\\
y \\
-x \cdot sin\space\theta + z \cdot cos\space\theta\\
1 \\
\end{array}
\right)
$$

Rotation around the z-axis:
$$
\left[
\begin{array}{cc}
cos\space\theta & -sin\space\theta & 0 & 0\\
sin\space\theta & cos\space\theta & 0 & 0 \\
0 & 0 & 1 & 0 \\
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
x \cdot cos\space\theta - y \cdot sin\space\theta\\
x \cdot sin\space\theta + y \cdot cos\space\theta\\
z \\
1 \\
\end{array}
\right)
$$

Using the rotation matrices, we can transform our position vectors around one of the three unit axes. To rotate around an arbitrary 3D axis, we can combine all 3 of them by first rotating around the x-axis, then the y-axis, then the z-axis. However, this introduces a problem call Gimbal lock.

A better solution is to rotate them around an arbitrary unit axis (e.g. 0.662, 0.2, 0,722) right away instead of combining the rotation matrices.

To truly prevent Gimbal locks, we have to represent rotations using quaternions.