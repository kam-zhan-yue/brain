Shaders are nice little programs, but they are part of a whole and need to have inputs and outputs. GLSL defines the `in` and `out` keywords for this purpose. Each shader can specify inputs and outputs using those keywords and wherever an output variable matches with an input variable of the next shader stage they're passed along.

The vertex shader **should** receive some form of input. The vertex shader differs in its input, in that it receives it straight from the vertex data. To define how the vertex data is organised, we specify the input variables with location metadata so that we can configure the vertex attributes on the CPU.
```c++
layout (location = 0)
```

The vertex shader thus requires an extra layout specification for its inputs so we can link it with the vertex data.

> It's also possible to omit the `layout (location = 0)` specifier and query or the attribute locations in your OpenGL code using `glGetAttribLocation`.

The fragment shader requires a `vec4` colour output variable, since the fragment shaders need to generate a final output colour. If you fail to specify an output colour in the fragment shader, the colour buffer output for those fragments will be undefined (which means they are rendered as black or white).

To send data from one shader to another, we have to declare an output in the sending shader and a similar input in the receiving shader. This is done when linking a program object.

We can alter the shaders to let the vertex shader decide the colour for the fragment shader.

### Vertex Shader
```c++
#version 330 core
layout (location = 0) in vec3 aPos; // position as attribute position 0

out vec4 vertexColour; // colour output for the fargment shader

void main() {
	gl_Position = vec4(aPos, 1.0);
	vertexColour = vec4(0.5, 0.0, 0.0, 1.0);
}
```

### Fragment Shader
```c++
out vec4 FragColour;

in vec4 vertexColour; // same type and name means it is passed through

void main() {
	FragColor = vertexColour;
}
```

Since `vertexColour` has the same type and name, the `vertexColour` in the vertex shader is linked to the `vertexColour` in the fragment shader.