The last thing to discuss when rendering vertices is the element buffer object (EBO). Suppose we want to draw a rectangle instead of a triangle. We can draw a rectangle using two triangles (OpenGL mainly works with triangles). 

```c++
float vertices[] = {
	// first triangle
	0.5f,  0.5f, 0.0f, // top right
	0.5f, -0.5f, 0.0f, // bottom right
	-0.5f,  0.5f, 0.0f, // top left
	// second triangle
	0.5f, -0.5f, 0.0f, // bottom right
	-0.5f, -0.5f, 0.0f, // bottom left
	-0.5f,  0.5f, 0.0f// top left
};
```

There is some overlap with the vertices specified. We specify `bottom right` and `top left` twice. This is an overhead of 50% since the same rectangle could also be specified with only 4 vertices, instead of 6. This will only get worse with more complex models that have over 1000s of triangles with overlap. A better solution would be to store only the unique vertices and then specify the order at which we want to draw these vertices in. 

Element buffer objects work exactly like this. An EBO is a buffer, just like a vertex buffer object, that stores indices that OpenGL uses to decide what vertices to draw.

This so called *indexed drawing* is the solution to the above problem. We have to specify the unique vertices and the indices to draw the as a rectangle.

```c++
float vertices[] = {
	0.5f,  0.5f, 0.0f, // top right
	0.5f, -0.5f, 0.0f, // bottom right
	-0.5f, -0.5f, 0.0f, // bottom left
	-0.5f,  0.5f, 0.0f// top left
}

float indices[] = {
	0, 1, 3, // first triangle
	1, 2, 3, // second triangle
}
```

When using indices, we only need 4 vertices instead of 6. Then we need to create the element buffer object.

```c++
unsigned int EBO;
glGenBuffers(1, &EBO);
```

Similar to the VBO, we bind the EBO and copy the indices into the buffer with `glBufferData`. Just like the VBO, we want to place those calls between a bind and an unbind call, but we specify `GL_ELEMENT_ARRAY_BUFFER` as the buffer type.

```c++
glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);
```

Now we are giving `GL_ELEMENT_ARRAY_BUFFER` as the buffer target. The last thing to do is to replace the `glDrawArrays` call with `glDrawElements` to indicate we want to render the triangle from an index buffer. When using `glDrawElements`, we're going to draw using indices provided in the element buffer object currently bound.

```c++
glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);
```

- The first argument specifies the mode we want to draw in
- The second argument is the count or number of elements we'd like to draw. We specify 6 indies to we want to draw 6 vertices in total
- The third argument is the type of indices which is of type `GL_UNSIGNED_INT`
- The last argument allows us to specify an offset in the EBO

The `glDrawElements` function takes its indices from the EBO currently bound to the `GL_ELEMENT_ARRAY_BUFFER` target. This means we have to bind the corresponding EBO each time we want to render an object with indices which can be cumbersome.

It just so happens that a vertex array object also keeps track of element buffer object bindings. The last element buffer object that gets bound while a VAO is bound, is stored as the VAO's element buffer object.

Binding to a VAO then automatically binds that EBO. Hence the order is:
- Bind VAO
- Bind EBO

> A VAO stores the `glBindBuffer` call when the target is `GL_ELEMENT_ARRAY_BUFFER`. This also means it stores its unbind calls so make sure you don't unbind the element array buffer before unbinding your VAO, otherwise it won't have the EBO configured.

The resultant initialisation looks like this:
```c++
// 1. bind vertex array object
glBindVertexArray(VAO);

// 2. copy our vertices array in a vertex buffer for OpenGL to use
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

// 3. copy our index array in an element buffer for OpenGL to use
glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);

// 4. then set the vertex attribute pointers for the shader
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);

// in the rendering loop
glUseProgram(shaderProgram);
glBindVertexArray(VAO);
glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);
glBindVertexArray(0); // unbind
```




