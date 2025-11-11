An alternative to `glVertexAttribPointer` is to batch all the vertex data into large chunks per attribute type instead of interleaving them.

That it, instead of `123123123`, we do `111222333`.

When loading vertex data from a file, we retrieve an array of positions, an array of normals and/or an array of texture coordinates. It may cost effort to combine these arrays into one large array of interleaved data. The batching approach is an easier solution.

```c++
float positions[] = { ... };
float normals[] = { ... };
float tex[] = { ... };
glBufferSubData(GL_ARRAY_BUFFER, 0, sizeof(positions), &positions);
glBufferSubData(GL_ARRAY_BUFFER, sizeof(positions), sizeof(normals), &normals);
glBufferSubData(GL_ARRAY_BUFFER, sizeof(normals), sizeof(tex), &tex);
```

In the above, we directly transfer the attribute arrays as a whole into the buffer without first having to process them. We can also combine them in one large array and fill in the buffer right away, but `glBufferSubData` lends itself perfectly for tasks like this.

We have to update the vertex attribute pointers though.
```c++
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), 0);
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), 0, (void*)(sizeof(positions)));
glVertexAttribPointer(2, 2, GL_FLOAT, GL_FALSE, 2 * sizeof(float), 0, (void*)(sizeof(positions) + sizeof(normals)));
```

The `stride` parameter is equal to the size of the vertex attribute, since the next vertex attribute vector can be found directly adjacent.