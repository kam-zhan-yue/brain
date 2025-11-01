We created a uniform material struct in the fragment shader, so we want to change the lighting calculations to comply with the new material properties.

We can set the material of the object in the application by setting the appropriate uniforms. A struct in GLSL is not special in any regard when setting uniforms; a struct only acts as a namespace of uniform variables.

```c++
shader.setVec3("material.ambient", 1.0f, 0.5f, 0.31f);
shader.setVec3("material.diffuse", 1.0f, 0.5f, 0.31f);
shader.setVec3("material.specular", 0.5f, 0.5f, 0.5f);
shader.setFloat("material.shininess", 32.0f);
```