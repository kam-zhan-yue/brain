The fragment shader is all about calculating the colour output of your pixels. To keep things simple, the fragment shader will always output an orange-ish colour.

```glsl
#version 330 core
out vec4 FragColor;

void main() {
	FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f);
}
```

The fragment shader only requires one output variable, a `vec4` that defines the final colour output that we calculate ourselves. We can declare output values with the `out` keyword. We simply assigned a `vec4` to the colour output as an orange colour.

The process for compiling a fragment shader is similar to the vertex shader, but we use the `GL_FRAGMENT_SHADER` constant as the shader type.

```c++
unsigned int fragmentShader;
fragmentShader = glCreateShader(GL_FRAGMENT_SHADER);
glShaderSource(fragmentShader, 1, &fragmentShaderSource, NULL);
glCompileShader(fragmentShader);
```

Now that both shaders are compiled, the only thing left to do is link both shader objects into a shader program that we can use for rendering.

## Shader Program

A shader program is the final linked version of multiple shaders combined. To use the recently compiled shaders, we have to link them to a shader program object and then activate this shader program when rendering objects. The activated shader program's shaders will be used when we issue render calls.

When linking the shaders into a program, it links the outputs of each shader to the inputs of the next shader. This is also where you'll get inking errors if your outputs and inputs do not match.

Creating a program object is as follows:

```c++
unsigned int shaderProgram;
shaderProgram = glCreateProgram();
```

The `glCreateProgram` function creates a program and returns the ID reference to the newly created program object. Now, we need to attach the previously compiled shaders to the program objet and then link them with `glLinkProgram`.

```c++
glAttachShader(shaderProgram, vertexShader);
glAttachShader(shaderProgram, fragmentShader);
glLinkProgram(shaderProgram);
```

Just like shader program, we can also check if linking a shader program failed and retrieve the corresponding log.

```c++
glGetProgramiv(shaderProgram, GL_LINK_STATUS, &success);
if (!success) {
	glGetProgramInfoLog(shaderProgram, 512, NULL, infoLog);
}
```

The result is a program object that we can activate by calling `glUseProgram` with the newly created program object as its argument.

Every shader and rendering call after `glUseProgram` will now use this program object (and thus its shaders).

Don't forget to delete the shader objects once we've linked them into the program.

We sent the input vertex data into the GPU and instructed the GPU how it should process the vertex data within a vertex and fragment shader. OpenGL does not yet know how it should interpret the vertex data in memory and how it should connect the vertex data to the vertex shader's attributes.