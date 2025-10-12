We can use the rectangle shape drawn with `glDrawElements` witht the texture.

```c++
float vertices[] = {
	// positions        // colors         // texture coords
	 0.5f,  0.5f, 0.0f, 1.0f, 0.0f, 0.0f, 1.0f, 1.0f, // top right
	 0.5f, -0.5f, 0.0f, 0.0f, 1.0f, 0.0f, 1.0f, 0.0f, // bottom right
	-0.5f, -0.5f, 0.0f, 0.0f, 0.0f, 1.0f, 0.0f, 0.0f, // bottom left
	-0.5f,  0.5f, 0.0f, 1.0f, 1.0f, 0.0f, 0.0f, 1.0f// top left
};
```

Since we've added an extra vertex attribute, we need to notify OpenGL of the new vertex format.

![[7-6-vertex.png]]

```c++
glVertexAttribPointer(2, 2, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void*)(6 * sizeof(float)));
glEnableVertexAttribArray(2);
```

Next, we need to alter the vertex shader to accept the texture coordinates as a vertex attribute and then forward the coordinates to the fragment shader.

```glsl
#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aColour;
layout (location = 2) in vec3 aTexCoord;

out vec3 ourColour;
out vec2 TexCoord;

void main() {
	gl_Position = vec4(aPos, 1.0);
	outColour = aColour;
	TexCoord = aTexCoord;
}
```

The fragment shader should then accept the `TexCoord` output variable as an input variable. In order to pass the texture object to the fragment shader, GLSL has a build-in data-type for texture objects called a sampler that takes a postfix the texture type we want (e.g. `sampler2D`).

We can then add a texture to the fragment shader by simply declaring a `uniform sampler2D` that we can later assign our texture to.

```c++
#version 330 core
out vec4 FragColour;

in vec3 ourColour;
in vec2 TexCoord;

uniform sampler2D ourTexture;

void main() {
	FragColour = texture(ourTexture, TexCoord);
}
```

To sample the colour of a texture we use GLSL's build-in `texture` function that takes a texture sample as its first argument and the texture coordinates as its second argument. The `texture` function then samples the corresponding colour value using the texture parameters set earlier. The output of this fragment shader is then the filtered colour of the texture at the interpolated texture coordinate.

Then, we need to bind the texture before calling `glDrawElements` and it will automatically assign the texture to the fragment shader's sampler:

```c++
glBindTexture(GL_TEXTURE_2D, texture);
glBindVertexArray(VAO);
glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);
```