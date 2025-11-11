Between the vertex and the fragment shader, there is an optional shader stage called the geometry shader. This takes an input a set of vertices that form a single primitive (a point or a triangle). The geometry shader can then transform these vertices before sending them to the next shader stage. It is able to convert the original primitive to completely different primitives, possibly generating more vertices than initially given.

An example of a geometry shader is

```glsl
#version 330 core

layout (points) in;
layout (line_strip, max_vertices = 2) out;

void main() {
	gl_Position = gl_in[0].gl_Position + vec4(-0.1, 0,0, 0,0, 0.0);
	EmitVertex();
	
	gl_Position = gl_in[0].gl_Position + vec4(0.1, 0.0, 0.0, 0.0);
	EmitVertex();
	
	EndPrimitive();
}
```

We specify the type of primitive input we're receiving
- points: when drawing GL_POINTS
- lines: when drawing GL_LINES
- triangles: when drawing GL_TRIANGLES

We specify the primitive type that the geometry shader will output
- points
- line_strip
- triangle_strip

With these 3 output specifiers, we can create almost any shape we want from the input primitives. The geometry shader also expects us to set a maximum number of vertices it outputs.

To generate meaningful results, we need some way to retrieve the output from the previous shader. GLSL gives us a built-in variable `gl_in` that internally looks like:

```glsl
in gl_Vertex {
	vec4 gl_Position;
	float gl_PointSize;
	float gl_ClipDistance[];
} gl_in[];
```

# Creating Vertices
EmitVertex and EndPrimitive can create new vertices.
- EmitVertex: each time we call this function, the vector currently set to `gl_Position` is added to the output primitive.
- EndPrimitive: each time we call this function, all emitted vertices for this primitive are combined into the specified output render primitive. By repeatdly calling this funciton, after one or more ExitVertex calls, multiple primitives can be generated.

