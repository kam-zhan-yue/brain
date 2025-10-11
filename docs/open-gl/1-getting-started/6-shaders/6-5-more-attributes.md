Using a VBO, we can configure vertex attribute pointers and store them in a VAO. We can also add colour data to the vertex data.

Let's try to assign a red, green, and blue colour to each of the colours of the triangle respective.

```c++
float vertices[] = {
	// positions          // colours
	-0.5f, -0.5f, 0.0f,   1.0f, 0.0f, 0.0f,
	0.5f, -0.5f, 0.0f,    0.0f, 1.0f, 0.0f,
	0.0f, 0.5f, 0.0f,     0.0f, 0.0f, 1.0f,
};
```

We also need to adjust the vertex shader to also receive colour value as a vertex attribute input.

The vertex shader then looks like

```c++
#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aColour;

out vec3 ourColour;

void main() {
	gl_Position = vec4(aPos, 1.0);
	ourColour = aColour;
}
```

And the fragment shader like
```c++
#version 330 core
in vec3 ourColour;
out vec4 FragColour;

void main() {
	FragColour = vec4(ourColour, 1.0);
}
```

Since we added another vertex attribute and updated the VBO's memory, we have to re-configure the vertex attribute pointers. The updated data in the VBO's memory now looks like
- Stride of 24 bytes (6x 4-byte floats)
- 12 bytes of pos, 12 bytes of colour, 12 bytes of pos, etc etc
- The stride between the position and colour are of 24 bytes

```c++
// position attribute
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float) (void*)0);
glEnableVertexAttribArray(0);

// colour attribute
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float) (void*)(3 * sizeof(float)));
glEnableVertexAttribArray(1);
```

- The first few arguments of `glVertexAttribPointer` are straightforward
	- Configure the vertex attribute on attribute location 1
	- The colour values have a size of 3 `floats` and we do not normalise the values
- Since we now have two vertex attributes, we have to re-calculate the *stride* value. To get the next attribute value (the next x component of the position vector) in the data array, we have to move 6 floats to the right, three for the position values and three for the colour values.
- This gives us a stride value of 6 times the size of a float in bytes.
- We also have to specify an offset. For each vertex, the position vertex attribute is first so we declare an offset of 0.
- The colour attribute starts after the position data, so the offset is `3 * sizeof(float)`

## Fragment Interpolation
This will produce a triangle with interpolated colours, even though we supplied only three colours. This is due to **fragment interpolation** in the fragment shader. When rendering a triangle in the rasterisation stage, it usually results in a lot more fragments than vertices originally specified.

The rasteriser then determines the positions of each of those fragments based on where they reside in the triangle shape. Based on these positions, it interpolates all the fragment shader's input variables.

Say for example we have a line where the upper point has a green colour and the lower point a blue colour. If the fragment shader is run at a fragment that resides around a position 70% of the line, its resulting colour input attribute would then be a linear combination of green and blue.

There are 3 vertices and 3 colours. The fragment shader interpolated the colours among the pixels. Fragment interpolation is applied to all the fragment shader's input attributes.

