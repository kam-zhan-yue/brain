We can use the geometry shader to build a house at the location of each point. We set the output of the geometry shader to `triangle_strip` and draw a total of three triangles: two for the square house and one for the roof.

A triangle strip in OpenGL is a more efficient way to draw triangles with fewer vertices. After the first triangle is drawn, each subsequent vertex generates another triangle next to the first triangle: every 3 adjacent vertices will form a triangle. If we have a total of 6 vertices that form a triangle strip, we'd get (1, 2, 3), (2, 3, 4), (3, 4, 5), and (4, 5, 6), forming 4 triangles. A triangle strip needs at least 3 vertices and will generate N-2 triangles.

![[30-2-triangle-strip.png]]

Using a triangle strip, we can easily output the house shape by generating 3 adjacent triangles in the correct order. 

![[30-2-house.png]]

```glsl
#version 330 core

layout (points) in;
layout (triangle_strip, max_vertices = 5) out;

void build_house(vec4 position) {
  gl_Position = position + vec4(-0.2, -0.2, 0.0, 0.0); // bottom-left
  EmitVertex()
  gl_Position = position + vec4(0.2, -0.2, 0.0, 0.0); // bottom-right
  EmitVertex()
  gl_Position = position + vec4(-0.2, 0.2, 0.0, 0.0); // top-left
  EmitVertex()
  gl_Position = position + vec4(0.2, 0.2, 0.0, 0.0); // top-right
  EmitVertex()
  gl_Position = position + vec4(0, 0.4, 0.0, 0.0); // top
  EmitVertex()
}


void main() {
  build_house(gl_in[0].gl_Position);
}

```

This generates 5 vertices, with each vertex being the point's position plus and offset to form one large triangle strip. The resulting primitive is then rasterised and the fragment shader runs on the entire triangle strip, resulting in a green house for each point rendered.

In order to give the houses a unique colour, we can add an extra vertex attribute in the vertex shader with colour information per vertex and forward it to the fragment shader. We can add this to the geometry shader to further forward it to the fragment shader.

Because the geometry shader acts on a set of vertices as its input, its input data from the vertex shader is always represented as array of vertex data (even though there is only one vertex right now).

In the geometry shader, we do:
```glsl
void build_house(vec4 position) {
  // IMPORTANT! DO BEFORE EMITTING VERTICES
  gs_out.colour = gs_in[0].colour;

  gl_Position = position + vec4(-0.2, -0.2, 0.0, 0.0); // bottom-left
  EmitVertex();
  gl_Position = position + vec4(0.2, -0.2, 0.0, 0.0); // bottom-right
  EmitVertex();
  gl_Position = position + vec4(-0.2, 0.2, 0.0, 0.0); // top-left
  EmitVertex();
  gl_Position = position + vec4(0.2, 0.2, 0.0, 0.0); // top-right
  EmitVertex();
  gl_Position = position + vec4(0, 0.4, 0.0, 0.0); // top
  EmitVertex();

  EndPrimitive();
}
```

It is vital to set the block before emitting vertices. When emitting a vertex, the vertex will store the last stored value in the interface blocks.