The vertex shader is one of the shaders available to be programmed. The first thing we need to do is write the vertex shader in GLSL and then compile this shader so that we can use it in our application.

```glsl
#version 330 core
layout (location = 0) in vec3 aPos;

void main() {
   gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);
}
```

- GLSL looks similar to C. Each shader begins with a declaration of its version. Since OpenGL 3.3 and higher, the version numbers of GLSL match the version of OpenGL. We also explicitly mention we're using core profile functionality.
- Next we declare all the input vertex attributes in the vertex shader in the `in` keyword. Right now, we only care about position data, so we only need a single vertex attribute. GLSL has a vector datatype that contains 1 to 4 floats based on its postfix digit. Since each vertex has a 3D coordinate we create a `vec3` input variable with the name `aPos`.
- We also specifically set the location of the input variable via `layout (location = 0)`
- To set the output of the vertex shader, we have to assign the position data to the predefined `gl_Position` variable which is a `vec4` behind the scenes. At the end of the `main` function, whatever we set `gl_Position` to will be used as the output of the vertex shader.
- Since our input is a vector of size 3, we have to cast this to a vector of size 4.

The current vertex shader is probably the most simple vertex shader we can imagine because we did no processing whatsoever on the input data and simply forwarded it to the shader's output.

In real applications, the input data is usually not already in normalised device coordinates, so we first have to transform the input data to coordinates that fall within OpenGL's visible region.

