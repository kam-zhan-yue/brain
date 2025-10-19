If we define a coordinate space using 3 perpendicular axes, you can create a matrix with those 3 axes plus a translation vector ad we can transform any vector to that coordinate space by multiplying it with this matrix. This is exactly that the `LookAt` matrix does now that we have the 3 perpendicular axes and a position vector.

$$
LookAt = 
\left[
\begin{array}{cc}
R_x & R_y & R_z & 0 \\
U_x & U_y & U_z & 0 \\
D_x & D_y & D_z & 0 \\
0 & 0 & 0 & 1 \\
\end{array}
\right]

\cdot

\left[
\begin{array}{cc}
1 & 0 & 0 & -P_x \\
0 & 1 & 0 & -P_y \\
0 & 0 & 1 & -P_z \\
0 & 0 & 0 & 1 \\
\end{array}
\right]
$$

- `R` is the right vector
- `U` is the up vector
- `D` is the direction vector
- `P` is the camera's position vector.

Note that the rotation (left matrix) and the translation (right matrix) parts are inverted (transposed and negated respectively) since we want to rotate and translate the world in the opposite direction of where we want the camera to move.

Using this LookAt matrix as our view matrix effectively transforms all the world coordinates to the view space as we just defined. The LookAt matrix then does exactly what it says: it creates a view matrix that *looks* at a given target.

GLM does this work for us. We only have to specify a camera position, a target position, and a vector that represents the up vector in world space.

```c++
glm::mat4 view;
view = glm::lookAt(glm::vec3(0.0f, 0.0f, 3.0f),
					glm::vec3(0.0f, 0.0f, 0.0f),
					glm::vec3(0.0f, 1.0f, 0.0f));
```

