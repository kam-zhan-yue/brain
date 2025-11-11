If a scene has a lot of models where the models contain the same set of vertex data but with different world transformations (e.g. grass), the scene may end up with tens of thousands of triangles that need to be rendered each frame. This may drastically reduce performance even if one leave of grass is only a few triangles.

Compared to rendering the actual vertices, telling the GPU to render your vertex data with functions like `glDrawArrays` or `glDrawElements` eats up performance since OpenGL must make necessary preparations before it can draw your vertex data. Even though rendering your vertices is fast, giving your GPU the render commands is not.

Through **instancing**, we can send data over to the GPU once and tell OpenGL to draw multiple objects with this data.

## Instancing
Instancing is a technique where we draw many (equal mesh data) objects at once with a single render call, saving us all the CPU -> GPU communications each time we need to render an object. To render using instancing, we need to change the render calls `glDrawArrays` and `glDrawElements` to `glDrawArraysInstanced` and `glDrawElementsInstanced` respectively. These *instanced* versions of the classic rendering functions take an extra parameter called the instance count that sets the number of instances we want to render.

We send all the required data to the GPU at once and tell the GPU how to draw these instances with a single call.

Using `gl_InstanceID`, we can index into a large array of position values to position each instance at a different location in the world (as an example).

```glsl
layout (location = 0) in vec2 aPosition;
layout (location = 1) in vec3 aColour;

out V_OUT {
  vec3 colour;
} v_out;

uniform vec2 offsets[100];

void main() {
  vec2 offset = offsets[gl_InstanceID];
  gl_Position = vec4(aPosition + offset, 0.0, 1.0);
  v_out.colour = aColour;
}
```

Instancing would then be:

```c++
  Shader shader = Shader(
    (string(SHADER_DIR) + "/vertex.glsl").c_str(), 
    (string(SHADER_DIR) + "/fragment.glsl").c_str()
  );
  shader.use();

  // Calculate Offsets
  glm::vec2 translations[100];
  int index = 0;
  float offset = 0.1f;
  for (int y=-10; y<10; y+=2) {
    for (int x=-10; x<10; x+= 2) {
      glm::vec2 translation;
      translation.x = (float)x / 10.0f + offset;
      translation.y = (float)y / 10.0f + offset;
      translations[index++] = translation;
    }
  }

  for (unsigned int i=0; i<100; ++i) {
    shader.setVec2("offsets[" + std::to_string(i) + "]", translations[i]);
  }

  ...
    glBindVertexArray(VAO);
    glDrawArraysInstanced(GL_TRIANGLES, 0, 6, 100);
    glBindVertexArray(0);

```

