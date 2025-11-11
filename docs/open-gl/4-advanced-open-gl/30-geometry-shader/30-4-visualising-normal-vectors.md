When programming lighting shaders, we will run into weird visual outputs. A common cause of lighting errors is incorrect normal vectors, either caused by incorrectly loading vertex data, improperly specifying them as vertex attributes, or buy incorrectly managing them in the shaders.

We can build a way to detect if the normal vectors supplied are correct. To visualise them:
- Build the scene as normal without a geometry shader and then draw the scene a second time, but only displaying normal vectors that we generate via a geometry shader.
- The geometry shader takes as input a triangle primitive and generates 3 lines from them in the direction of their normal - one normal for each vertex.

```c++
shader.use();
DrawScene();
normalDisplayShader.use()
DrawScene();
```

The geometry shader can use the vertex normals supplied by the model instead of generating it. To accommodate for scaling and rotations, we transform the normals with a normal matrix. The geometry shader receives its position vectors as view-space coordinates, so we should transform the normal vectors to the same space.

**Vertex Shader**
```glsl
out V_OUT {
  vec3 Normal;
} v_out;

void main()
{
  gl_Position = projection * view * model * vec4(aPos, 1.0);
  mat3 normalMatrix = mat3(transpose(inverse(view * model)));
  v_out.Normal = normalize(vec3(vec4(normalMatrix * aNormal, 0.0)));
}
```

**Geometry Shader**
The geometry shader then takes each vertex and draws a normal vector from the position vector.
```glsl
void generateLine(int index) {
  vec4 vertexPos = gl_in[index].gl_Position;

  // Origin Point
  gl_Position = projection * vertexPos;
  EmitVertex();

  // Origin Point + Normal Vector
  gl_Position = projection * (vertexPos + vec4(g_in[index].normal, 0.0) * MAGNITUDE);
  EmitVertex();

  EndPrimitive();
}

void main() {
  generateLine(0);
  generateLine(1);
  generateLine(2);
}
```

**Fragment Shader**
The fragment shader can be super simple.