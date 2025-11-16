To achieve the effect of an asteroid field, we need to generate a model transformation matrix for each asteroid. This translates the rock somewhere in the asteroid ring, then we add a small random displacement value to the offset to make the ring look more natural.

```c++
    unsigned int amount = 1000;
    glm::mat4 *modelMatrices;
    modelMatrices = new glm::mat4[amount];
    srand(glfwGetTime()); // initialise a random seed
    float radius = 50.0;
    float offset = 2.5;

    for (unsigned int i=0; i<amount; ++i) {
      // 1. Displacement along the circle
      float angle = (float)i / (float)amount * 360.0;
      float displacement = (rand() % (int)(2 * offset * 100)) / 100.0f - offset;
      float x = sin(angle) * radius + displacement;
      displacement = (rand() % (int)(2 * offset * 100)) / 100.0f - offset;
      float y = displacement * 0.4f;
      displacement = (rand() % (int)(2 * offset * 100)) / 100.0f - offset;
      float z = cos(angle) * radius + displacement;

      // 2. Scale and Rotation
      float scale = (rand() % 360);
      float rotation = (rand() % 360);
      
      glm::mat4 model = glm::mat4(1.0);
      model = glm::translate(model, vec3(x, y, z));
      model = glm::scale(model, vec3(scale));
      model = glm::rotate(model, rotation, glm::vec3(0.4f, 0.6f, 0.8f));
      modelMatrices[i] = model;
    }
```

Upon rendering all of these, we have a total of 1001 rendering calls per frame. As we increase this number, we notice that the scene stops running smoothly and the number of frames we can render per second reduces drastically.

We can then use instanced rendering instead.

```glsl
#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aNormal;
layout (location = 2) in vec2 aTexCoords;
layout (location = 3) in mat4 aModel;

uniform mat4 view;
uniform mat4 projection;

out V_OUT {
  vec3 normal;
  vec3 position;
  vec2 tex;
} v_out;

void main()
{
  gl_Position = projection * view * aModel * vec4(aPos, 1.0);
  v_out.position = vec3(aModel * vec4(aPos, 1.0));
  v_out.normal = mat3(transpose(inverse(aModel))) * aNormal;
  v_out.tex = aTexCoords;
}
```


Instead of a `uniform mat4 model`, we use a vertex attribute. However, when we declare a datatype as a vertex attribute that is greater than `vec4`, things work a bit differently. The maximum amount of data allowed for a vertex attribute is equal to a `vec4`. However, since a `mat4` is basically 4x `vec4`s, we have to reserve 4 vertex attributes to this specific matrix.