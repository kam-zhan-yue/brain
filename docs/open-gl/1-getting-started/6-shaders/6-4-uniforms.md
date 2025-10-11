Uniforms are another way to pass data from our application on the CPU to the shaders on the GPU. Uniforms are slightly different to vertex attributes.
- Uniforms are **global**, meaning that a uniform variable is unique per shader program object, and can be access from any shader at any stage in the shader program.
- Whatever you set the uniform shader value to, uniforms will keep their values until they are reset or updated.

To declare a uniform in GLSL, we simply add the `uniform` keyword to a shader with a type and name. From that point, we can use the newly declared uniform in the shader.

```c++
#version 330 core
out vec4 FragColour;
uniform vec4 ourColour;

void main() {
	FragColour = ourColour;
}
```

Since uniforms are global variables, we can define them in any shader stage we'd like, so no need to go through the vertex shader again to get something to the fragment shader.

> If you declare a uniform that isn't used anywhere in your GLSL code, the compiler will silently remove the variable from the compiled version. This is the cause for several frustrating errors.

The uniform is empty until you add data to it. To add data, you need to find the index/location of the uniform attribute in the shader.

Once we get the index/location of the uniform, we can update its values. Instead of passing a single colour, we can gradually change the colour over time.

```c++
float timeValue = glfwGetTime();
float greenValue = (sin(timeValue) / 2.0f) + 0.5f;

int vertexColourLocation = glGetUniformLocation(shaderProgram, "ourColour");
glUseProgram(shaderProgram);
glUniform4f(vertexColourLocation, 0.0f, greenValue, 0.0f, 1.0f);
```
- First we retrieve the running time in seconds with `glfwGetTime`
- Then we vary the colour in the range of 0.0 - 1.0
- Then we query for the location of the `ourColour` using `glGetUniformLocation`
- We supply the shader program and the name of the uniform into the query function
- If `glGetUniformLocation` returns `-1`, it could not find the location.
- Finally, we set the uniform value using `glUniform4f`

> Finding the uniform location does not require you to use the shader program first. However, updating a uniform does require you to use the program first because it sets the uniform on the currently active shader program.