When we are rendering more than 100 instances, we eventually hit a limit on the amount of uniform data we are allowed to send to shaders. The alternative is using **instanced arrays**. These are defined as a vertex attribute (allowing us to use more data) that are updated per instance instead of per vertex.

At the start of each run of the vertex shader, the GPU will retrieve the next set of vertex attributes that belong to the current vertex. When defining a vertex attribute as an instanced array, the vertex shader only updates the content of the vertex attribute per instance. This allows us to use the standard vertex attributes for data per vertex and use the instanced array for storing data that is unique per instance.

An example of an instanced array is:

```glsl
#version 330 core

layout (location = 0) in vec2 aPosition;
layout (location = 1) in vec3 aColour;
layout (location = 2) in vec2 aOffset;

out V_OUT {
  vec3 colour;
} v_out;

uniform vec2 offsets[100];

void main() {
  gl_Position = vec4(aPosition + aOffset, 0.0, 1.0);
  v_out.colour = aColour;
}
```

We no longer use `gl_InstanceID` and instead use `aOffset`.

```c++
  // Generating and Binding
  unsigned int VAO, VBO;
  glGenVertexArrays(1, &VAO);
  glGenBuffers(1, &VBO);

  glBindVertexArray(VAO);
  glBindBuffer(GL_ARRAY_BUFFER, VBO);

  glBufferData(GL_ARRAY_BUFFER, sizeof(quadVertices), quadVertices, GL_STATIC_DRAW);
  glEnableVertexAttribArray(0);
  glVertexAttribPointer(0, 2, GL_FLOAT, GL_FALSE, 5 * sizeof(float), (void*)0);
  glEnableVertexAttribArray(1);
  glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 5 * sizeof(float), (void*)(2 * sizeof(float)));

  // Instanced Array
  unsigned int instanceVBO;
  glGenBuffers(1, &instanceVBO);
  glBindBuffer(GL_ARRAY_BUFFER, instanceVBO);
  glBufferData(GL_ARRAY_BUFFER, sizeof(glm::vec2) * 100, &translations[0], GL_STATIC_DRAW);

  glEnableVertexAttribArray(2);
  glBindBuffer(GL_ARRAY_BUFFER, instanceVBO);
  glVertexAttribPointer(2, 2, GL_FLOAT, GL_FALSE, 2 * sizeof(float), (void*)0);

  // Tells OpenGL when to update the content of a vertex attribute to the next element
  glVertexAttribDivisor(2, 1);

  glBindVertexArray(0);
  glBindBuffer(GL_ARRAY_BUFFER, 0);

```

The `glVertexAttribDivisor` function takes two parameters:
- The first parameter is the vertex attribute in question
- The second parameter is the attribute divisor
By default, the attribute divisor is 0, which tells OpenGL to update the content of the vertex attribute each iteration of the vertex shader. By setting this attribute to 1, we tell OpenGL that we want to update the content of the vertex attribute when we start to render a new instance. If we set it to 2, we'd update the content every 2 instances, etc.

TLDR: this function tells OpenGL that the vertex attribute at location 2 is an instanced array that will update it's value for every 1 instance is drawn.

We can slowly downscale each quad from top-right to bottom-left using `gl_InstanceID`