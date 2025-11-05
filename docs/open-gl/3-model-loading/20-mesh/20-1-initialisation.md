We can then define the structure of the mesh class:

```c++
class Mesh {
	public:
		// mesh data
		vector<Vertex> vertices;
		vector<unsigned int> indices;
		vector<Texture> textures;
		
		Mesh(vector<Vertex> vertices, vector<unsigned int> indices, vector<Texture> textures);
		
		void Draw(Shader &shader);
		
		// render data;
		unsigned int VAO, VBO, EBO;
		void setupMesh();
}
```

We can setup the mesh as so

```c++
  void setupMesh() {
    // 1. Generate GLFW Buffers
    glGenVertexArrays(1, &VAO);
    glGenBuffers(1, &VBO);
    glGenBuffers(1, &EBO);

    // 2. Populate data into the VAO and VBO
    glBindVertexArray(VAO);
    glBindBuffer(GL_ARRAY_BUFFER, VBO);
    glBufferData(GL_ARRAY_BUFFER, vertices.size() * sizeof(Vertex), &vertices[0], GL_STATIC_DRAW);

    // 3. Populate data into the EBO
    glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
    glBufferData(GL_ELEMENT_ARRAY_BUFFER, indices.size() * sizeof(unsigned int), &indices[0], GL_STATIC_DRAW);

    // 4. Set the vertex positions, normals, and texture coords
    glEnableVertexAttribArray(0);
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, sizeof(Vertex), (void*)0);

    glEnableVertexAttribArray(1);
    glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, sizeof(Vertex), (void*)offsetof(Vertex, Normal));

    glEnableVertexAttribArray(2);
    glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, sizeof(Vertex), (void*)offsetof(Vertex, TexCoords));

    glBindVertexArray(0);
  }
```

The memory layout of structs in C++ is sequential. This means if we were to represent a struct as an array of data, it would only contain the struct's variables in sequential order which directly translates to a byte array that we want for an array buffer. For example, if we have a filled `Vertex` struct, its memory layout would be equal to:

```c++
Vertex vertex;
vertex.Position = vec3(0.2f, 0.4f, 0.6f);
vertex.Normal = vec3(0.0f, 1.0f, 0.0f);
vertex.TexCoords = vec2(1.0f, 0.0f);
// = [0.2f, 0.4f, 0.6f, 0.0f, 1.0f, 0.0f, 1.0f, 0.0f]
```

Thanks to this, we can directly pass a pointer to a large list of `Vertex` structs as the buffer's data and they translate perfectly to what `glBufferData` expects as its argument.

Another great use of structs is a preprocessor directive called `offsetof(s, m)` that takes as its first argument a struct and as a second argument the variable name of the struct. The macro returns the byte offset of that variable from the start of the struct.