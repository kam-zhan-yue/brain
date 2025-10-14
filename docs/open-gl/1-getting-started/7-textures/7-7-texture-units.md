The previous code worked even though `sampler2D` wasn't assigned any value using `glUniform`. Using `glUniform1i`, we can actually assign a *location* value to the texture sampler so we can set multiple textures at once in a fragment shader.

The location of a texture is known as a texture unit. The default unit for a texture is 0, which is the default active texture unit, so we didn't need to assign a location in the previous code.

> Not all graphics drivers assign a default texture

The main purpose of texture units is to allow us to use more than 1 texture in our shaders. By assigning texture units to the samplers, we can bind to multiple textures at once as long as we activate the corresponding texture unit first. Just like `glBindTexture`, we can activate texture units using `glActiveTexture` passing in the texture unit we'd like to use

```c++
glActiveTexture(GL_TEXTURE0)
glBindTexture(GL_TEXTURE_2D, texture);
```

After activating a texture unit, a subsequent `glBindTexture` call will bind that texture to the currently active texture unit. Texture unit `GL_TEXTURE0` is always active by default.

OpenGL has a minimum of 16 texture units to use.
```c++
GL_TEXTURE8 = GL_TEXTURE0 + 8
```

We would need to activate edit the fragment shader to accept another sampler.

```c++
#version 330 core
...
uniform sampler2D texture1;
uniform sampler2D texture2;

void main() {
	FragColor = mix(texture(texture1, TexCoord),
					texture(texture2, TexCoord), 0.2);
}
```

The final output colour is now the combination of two texture lookups. GLSL's built-in `mix` function takes two values as input and linearly interpolates between them basd on the third argument.
- If the value is 0.0, it returns the first input
- If the value is 1.0, it returns the second input
- A value of 0.2 returns 80% of the first input and 20% of the second input.

We can load a second image using
```c++
unsigned char *data = stbi_load("awesomeface.png", &width, &height, &nrChannels, 0);

if (data) {
	glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, width, height, 0, GL_RBGA, GL_UNSIGNED_BYTE, data);
	glGenerateMipmap(GL_TEXTURE_2D);
}
```

Loading `GL_RGBA` allows us to include an alpha channel.

To use the second texture, we have to change the rendering procedure:
```c++
glActiveTexture(GL_TEXTURE0);
glBindTexture(GL_TEXTURE_2D, texture1);
glActiveTexture(GL_TEXTURE1);
glBindTexture(GL_TEXTURE_2D, texture2);

glBindVertexArray(VAO);
glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);
```

We also have to tell OpenGL to which texture unit each shader sampler belongs to by setting each sampler using `glUniform1i`. We only need to do this once before entering the rendering loop.

```c++
shader.use(); // activate the shader first
glUniform1i(glGetUniformLocation(shader.ID, "texture1"), 0); // manually
shader.setInt("texture2", 1); // or with shader class
```

OpenGL expects the 0.0 coordinate on the y-axis to be the bottom side of the image, but images usually have 0.0 at the top of the y-axis. 

```c++
stbi_set_flip_vertically_on_load(true);
```