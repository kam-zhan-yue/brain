We want a way to set the diffuse colours of an object for each individual fragment. Some sort of system where we can retrieve a colour value based on the fragment's position on the object.

This is basically a texture. Using an image wrapped around an object that we can index for unique colour values per fragment. In lit scenes, this is usually called a diffuse map since a texture image represents all of the object's diffuse colours.

We can demonstrate diffuse maps with an image of a wooden container with a steel border.

![[15-1-wooden-container.png]]

Using a diffuse map in shaders is exactly like a texture. This time, we store the texture as a `sampler2D` inside the `Material` struct. We replace the earlier defined `vec3` diffuse with the diffuse map.

> Keep in mind that `sampler2D` is a so-called opaque type, which means we can't instantiate these types, but only define them as uniforms. If the struct would be instantiated other than as a uniform, GLSL could throw strange errors; the same applies to any struct holding such opaque types.

We also remove the ambient colour since the ambient colour is equal to the diffuse colour anyways now that we control ambient with the light.

```glsl
struct Material {
	 sampler2D diffuse;
	 vec3 specular;
	 float shininess;
}
```

Note that we are going to need texture coordinates again in the fragment shader, so we declared an extra input variable.

```glsl
vec3 textureColour = vec3(texture(material.diffuse, TexCoords));
vec3 ambient = textureColour * light.ambient;
vec3 diffuse = textureColour * light.diffuse * diff;
```

We also need to set the texture
```c++
shader.setInt("material.diffuse", 0);
glActiveTexture(GL_TEXTURE0);
glBindTexture(GL_TEXTURE_2D, diffuseMap):
```