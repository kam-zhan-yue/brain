We need to define a `draw` function to the Mesh class. Before rendering the mesh, we want to first bind its appropriate textures before calling `glDrawElements`. However, this is somewhat difficult since we don't know from the start how many (if any) textures the mesh has and what type they may have.

To solve this issue, we can assume a naming convention: each diffuse texture is named `texture_diffuseN` and each specular texture should be named `texture_specularN`.

```c++
uniform sampler2D texture_diffuse1;
uniform sampler2D texture_diffuse2;
uniform sampler2D texture_diffuse3;
uniform sampler2D texture_specular1;
uniform sampler2D texture_specular2;
```

The draw function would then be:

```c++
  void draw(Shader &shader) {
    unsigned int diffuseNum = 1;
    unsigned int specularNum = 1;

    for(unsigned int i=0; i<textures.size(); ++i) {
      glActiveTexture(GL_TEXTURE0 + i);
      string number;
      string name = textures[i].type;
      if (name == "texture_diffuse") {
        number = std::to_string(diffuseNum++);
      } else if (name == "texture_specular") {
        number = std::to_string(specularNum++);
      }

      shader.setFloat(("material." + name + number).c_str(), i);
      glBindTexture(GL_TEXTURE_2D, textures[i].id);
    }

    glActiveTexture(GL_TEXTURE0);

    // draw mesh
    glBindVertexArray(VAO);
    glDrawElements(GL_TRIANGLES, indices.size(), GL_UNSIGNED_INT, 0);
    glBindVertexArray(0);
  }
```

We first calculate the N-component per texture type and concatenate it to the texture's type string to get the appropriate uniform name. We then locate the appropriate sampler, give it the location value to correspond with the currently active texture unit, and bind the texture. This is also the reason we need the shader in the `Draw` function.

We also add `material.` to the resulting uniform name because we usually store the textures in a material struct.
