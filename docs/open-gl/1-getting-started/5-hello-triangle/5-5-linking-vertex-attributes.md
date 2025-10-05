The vertex shader allows us to specify any input we want in the form of vertex attributes. While this allows for great flexibility, it means we have to manually specify what part of our input data goes into which vertex attribute in the vertex shader. This means we have to specify how OpenGL should interpret the vertex data before rendering.

To render a triangle, we need three vertices. Each vertex has a position of 3 bytes.
- The position data is stored as 32-bit (4 byte) floating point values
- Each position is composed of 3 of those values (12 bytes)
- There is no space between each set of 3 values. The values are tightly packed
- The first value in the data is the beginning of the buffer.

With this knowledge, we can tell OpenGL how it should interpret the vertex data (per vertex attribute) using `glVertexAttribPointer`.

```c++
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);
```

- The first parameter specifies which vertex attribute we want to configure. We specified the location of the position vertex attribute in the vertex shader with `layout (location = 0)`. This sets the location of the vertex attribute to 0 and since we want to pass data to this vertex attribute, we pass in 0.
- The second parameter specifies the size of the vertex attribute. The vertex attribute is a `vec3` so it is composed of 3 values.
- The third parameter specifies the type of data, which is a `GL_FLOAT` (a `vec*` in GLSL consisting of floating points values).
- The fourth parameter specifies if we want the data to be normalised. If we're inputting integer data types and we set it to `GL_TRUE`, the integer data is normalised to 0 or -1 or 1 when converted to float. This is not relevant for us.
- The fifth parameter is known as the **stride** and tells us the space between consecutive vertex attributes. Since the next set of position data is located exactly 3 times the size of a `float` away, we specify that value as the stride. Whenever we have more vertex attributes, we have to carefully define the spacing between each vertex attribute.
- The last parameter is of type `void*`. This is the **offset** of where the position data begins in the buffer. Since the position data is at the start of the data array, this value is just 0.

> Each vertex attribute takes its data from memory managed by a VBO and which VBO it takes its data from is determined by the VBO currently bound to `GL_ARRAY_BUFFER` when calling `glVertexAttribPointer`. Since the previously defined VBO is still bound before calling `glVertexAttribPointer`, vertex attribute `0` is now associated with its vertex data.

After specifying how OpenGL should interpret the vertex data, we should also enable the vertex attribute with `glEnableVertexAttribArray` giving the vertex attribute location as its argument; vertex attributes are disabled by default. From that point, we have everything setup:
- We initialised the vertex data in a buffer using a vertex buffer object
- We setup a vertex and fragment shader and told OpenGL how to link the vertex data to the vertex shader's vertex attributes

Drawing an object in OpenGL would now look like:

```c++
// 0. copy our vertices array in a buffer for OpenGL to use
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

// 1. then set the vertex attribute pointers
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);

// 2. use our shader program when we want to render an object
glUseProgram(shaderProgram);

// 3. now draw the object)
someOpenGLFunctionThatDraws();
```

However, repeating this process every time we want to draw an object can be tedious.

## Vertex Array Object
A vertex array object (or VAO) can be bound just like a vertex buffer object and any subsequent vertex attribute calls from that point will be stored inside the VAO. This has the advantage that when configuring vertex attribute pointers, you only have to make those calls once and whenever we want to draw the object, we can just bind the corresponding VAO. This makes switching between different vertex data and attribute configurations as easy as binding a different VAO.

> Core OpenGL **requires** that we use a VAO so it knows what to do with our vertex inputs. If we fail to bind a VAO, OpenGL will most likely refuse to draw anything.

A vertex array object requires the following:
- Calls to `glEnableVertexAttribArray` or `glDisableVertexAttribArray`
- Vertex attribute configurations via `glVertexAttribPointer`
- Vertex buffer objects associated with vertex attributes by calls to `glVertexAttribPointer`

The process to generate a VAO is as follows:
```c++
unsigned int VAO;
glGenVertexArrays(1, &VAO);
```

To use a VAO, you need to bind the VAO using `glBindVertexArray`. From that point, we should bind/configure the corresponding VBOs and attribute pointers and then unbind the VAO for later use. As soon as we want to draw an object, we simply bind the VAO with the preferred settings before drawing the object.

```c++
// initialisation as above
glBindVertexArray(VAO);
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

// set our vertix attributes pointers
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);
glUseProgram(shaderProgram);
glBindVertexArray(VAO);
```

Finally, to draw the triangle, OpenGL provides us with the `glDrawArrays` function that draws primitives using the currently active shader, the previously defined vertex attribute configuration, and with the VBO's vertex data (indirectly bound to the VAO).

```c++
glUseProgram(shaderProgram);
glBindVertexArray(VAO);
glDrawArrays(GL_TRIANGLES, 0, 3);
```

- The `glDrawArrays` function takes as its first argument the OpenGL primitive type we would like to draw. 
- The second argument specifies the staring index of the vertex array we'd like to draw
- The last argument specifies how many vertices we want to draw, which is 3