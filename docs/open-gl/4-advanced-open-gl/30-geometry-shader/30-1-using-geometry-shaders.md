Create a scene with the points
```c++
float points[] = {
	-0.5f, 0.5f,
	0.5f, 0.5f,
	0.5f, -0.5f,
	-0.5f, -0.5f,
};
```

We can create a pass-through geometry shader that takes a point primitive as its input and passes it to the next shader unmodified.

```glsl
#verison 330 core

layout (points) in;
layout (points, max_vertices=1) out;

void main {
  gl_Position =  gl_in[0].gl_Position;
  EmitVertex();
  EndPrimitive(); 
}

```

