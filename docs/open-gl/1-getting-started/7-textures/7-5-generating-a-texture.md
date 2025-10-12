Textures in OpenGL are references with an ID after generation:
```c++
unsigned int texture;
glGenTextures(1, &texture);
```

Just like other objects, we need to bind it so that any subsequent texture commands will configure the currently bound texture:
```c++
glBindTexture(GL_TEXTURE_2D, texture);
```

Now that the texture is bound, we can start generating a texture using the previously loaded image data. Textures are generated with `glTexImage2D`:

```c++
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, width, height, 0, GL_RGB, GL_UNSIGNED_BYTE, data);
glGenerateMipmap(GL_TEXTURE_2D);
```

- The first argument specifies the texture target; setting this to `GL_TEXTURE_2D` means that this operation will generate a texture on the currently bound texture object
- The second argument specifies the mipmap level for which we want to create a texture for. The base level is 0
- The third argument tells OpenGL what kind of format we want to store the texture. The image only has RGB values
- The fourth and fifth argument sets the width and height of the resultant texture
- The sixth argument should always be 0 (legacy stuff)
- The seventh and eighth argument specify the format and datatype of the source image. We loaded the image with RGB values and stores them as `chars` (bytes)
- The last argument is the actual image data

Once `glTexImage2D` is called, the currently bound texture object now has the texture image attached to it. However, it only has the base-level of the texture image loaded and if we want to use mipmaps, we have to specify all the different images manually (by incrementing the second argument) or we can call `glGenerateMipmaps`. This will automatically generate the required mipmaps for the currently bound texture.

After generating the texture and mipmaps, we should free the image memory:

```c++
stbi_image_free(data);
```

The process of texture generation looks like

```c++
unsigned int texture;
glGenTextures(1, &texture);
glBindTexture(GL_TEXTURE_2D, texture);

// set the texture wrapping/filtering options
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);

// load and generate the texture
int width, height, nrChannels;
unsigned char *data = stbi_load("container.jpg", &width, &height, &nrChannels, 0);

if (data) {
	glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, width, height, 0, GL_RGB, GL_UNSIGNED_BYTE, data);
	glGenerateMipmap(GL_TEXTURE_2D);
} else {
	std::cout << "Failed to load texture" << std::endl;
}

stbi_image_free(data);

```

