To light a scene, we need light. We can render a light source cube. We want to generate a new VAO specifically for the light source. We could render the light source with the same VAO and then do a few light position transformations with the `model` matrix, but we will be changing the vertex data and attribute pointers of the normal cube quite often and have no reason to propagate it to the light source.

Then, we can define the fragment shader for both the container and the lightsource.

```c++
#version 330 core
out vec4 FragColor;

uniform vec3 objectColour;
uniform vec3 lightColour;

void main() {
	FragColor = vec4(lightColour * objectColour, 1.0);
}
```

When we want to render, we want to render the container object using the lighting shader as above, and we want to draw the light source with another shader (one that turns it all white).

The main purpose of the light source cube is to show where the light is coming from. We can render this on the cube itself.

```c++
glm::vec3 lightPos(1.2f, 1.0f, 2.0f);
model = glm::mat(1.0f);
model = glm::translate(model, lightPos);
model = glm::scale(model, glm::vec3(0.2f));


lightShader.use();
// set the model, view, and projection matrix uniforms
// draw the light cube object
glBindVertexArray(LIGHT_VAO);
glDrawArrays(GL_TRIANGLES, 0, 36);
```