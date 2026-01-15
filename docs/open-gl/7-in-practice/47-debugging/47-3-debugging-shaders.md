When it comes to GLSL, there is no `glGetError`. One frequently used trick to figure out what is wrong is to evaluate all relevant variables in a shader program by sending them directly to the fragment shader's output channel. By outputting shader variables like this, we can convey interesting information by analysing the visual results. 

For instance, let's say we want to check if a model as the correct normal vectors. We can pass them from the vertex shader to the fragment shader's colours.

```glsl
in vec3 normal;
void main() {
	FragColor = vec4(normal, 1.0);
}
```

If the visual result is completely black, it is clear the normal vectors aren't correctly passed to the shaders; and when they are displayed, it is relatively easy to check if they are correct or not.