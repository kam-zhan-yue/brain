We can take the source code of the vertex shader and store it in a const C string at the top of the code file.

```c++
const char *vertexShaderSource = "#version 330 core\n"
       "layout (location = 0) in vec3 aPos;\n"
       "void main()\n"
       "{\n"

       " gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n"
       "}\0";
```

In order for OpenGL to use the shader, it has to dynamically compile it at run-time from its source code. The first thing we need to do is create a shader object and store the vertex shader as an `unsigned int`.

```c++
unsigned int vertexShader;
vertexShader = glCreateShader(GL_VERTEX_SHADER)
```

Next, we attach the shader source code to the shader object and compile the shader.

```c++
glShaderSource(vertexShader, 1, &vertexShaderSource, NULL);
glCompileShader(vertexShader)
```

- The `glShaderSource` function takes the shader object to compile to as its argument.
- The second argument specifies now many strings we're passing as source code.
- The third parameter is the actual source code of the vertex shader.

Checking for compile-time errors is accomplished as follows:
```c++
int success;
char infoLog[512];
glGetShaderiv(vertexShader, GL_COMPILE_STATUS, &success);
```

- An integer is used to indicate success
- A storage container for the error messages (if any)
- Then we check if compilation was successful with `glGetShaderiv`
- If compilation failed, we should retrieve the error with `glGetShaderInfoLog`

```c++
if (!success) {
	glGetShaderInfoLog(vertexShader, 512, NULL, infoLog);
	std::cout << "ERROR:SHADER::VERTEX::COMPILATION_FAILED\n" << infoLog << std::endl;
}
```