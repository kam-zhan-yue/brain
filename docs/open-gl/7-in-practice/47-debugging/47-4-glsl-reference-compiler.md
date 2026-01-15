Each driver has its own tidbits and quirks. For instance, NVIDIA drivers are more flexible and tend to overlook some restrictions on the specification, while ATI/AMD drivers tend to better enforce the OpenGL specification. The result of this is that shaders on one machine may not work on the other due to driver differences.

To make sure that your shader code runs on all kinds of machines, you can directly check your shader code against the official specification using OpenGL's GLSL reference compiler. You can download the GLSL lang validator here: https://github.com/KhronosGroup/glslang

Given the binary GLSL lang validator, you can easily check your shader code by passing it as the binary's first argument. However, you need to abide to the shader extensions:
- `.comp`: compute shader
- `.vert`: vertex shader
- `.frag` fragment shader

Running the GLSL reference compiler is as simple as 
```shell
brew install glslang # ensure installed first
glslang shaderFile.vert
```

Example output: 

```shell
➜ glslang 7.in-practice/44.3.debugging-shaders/object.vert
7.in-practice/44.3.debugging-shaders/object.vert
ERROR: 0:18: 'gl_Psaosition' : undeclared identifier
ERROR: 0:18: '' : compilation terminated
ERROR: 2 compilation errors.  No code generated.
```

It won't show subtle difference between AMD, NVIDIA, or Intel GLSL compilers, but it does help to check shaders against the direct GLSL specifications.