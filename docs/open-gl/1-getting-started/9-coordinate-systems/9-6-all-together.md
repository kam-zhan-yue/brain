We can create a transformation matrix for each of the aforementioned steps: model, view, and projection matrix. A vertex coordinate is then transformed into clip coordinates as follows:

$$
V_{clip} = M_{projection} \cdot M_{view} \cdot M_{model} \cdot V_{local}
$$

Note that the order of matrix multiplication is reversed (we read matrix multiplication from right to left). The resulting vertex should then be assigned to `gl_Position` in the vertex shader and OpenGL will then automatically perform perspective division and clipping.

> The output of the vertex shader requires the coordinates to be in clip-space. OpenGL then performs *perspectiev division* on the *clip-space coordinates* to transform them to *normalised-device coordinates*. OpenGL then uses the parameters from `glViewPort` to map the NDCs to *screen coordinates* where each coordinate corresponds to a point on your screen. This process is called the *viewport transform*.
