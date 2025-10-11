GLSL is tailored for use with graphics and contains useful features specifically targeted at vector and matrix manipulation.

Shaders always begin with a version declaration, followed by a list of input and output variables, uniforms, and its `main` function. Each shader's entry point is at its `main` function where we process any input variables and output the results in its output variables. A shader typically has the following structure:

```c++
#version version_number
in type in_variable_name;
in type in_variable_name;

out type out_variable_name;

uniform type uniform_name;

void main() {
	// process inputs and do weird graphics stuff
	//output processed stuff to output variable
	out_variable_name = weird_stuff_we_processed
}
```

When talking about the vertex shader, each input variable is known as a vertex attribute. There is a maximum number of vertex attributes we're allowed to declare that is limited by the hardware. OpenGL guarantees that there are always at least 16 4-component vertex attributes available.