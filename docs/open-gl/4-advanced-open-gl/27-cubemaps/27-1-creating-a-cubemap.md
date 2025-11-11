A cubemap is a texture that combines 6 individual 2D textures that each form one side of a cube; a textured cube. Cubemaps have the useful property that they can be indexed / sampled using a direction vector. 

![[27-1-cubemap.png]]

Imagine we have a 1x1x1 unit cube with the origin of a direction vector at its centre. Sampling a texture value is easy with a simple vector.

If we have a cube shape that we can make a cubemap to, this direction vetor would be similar to the interpolated local vertex position of the cube. This way, we can sample the cubemap using the cube's actual position vectors as long as the cube is centred on the origin. We thus consider all vertex positions of the cube to be its texture coordinates. The result is a texture coordinate that accesses the proper individual face texture of the cubemap.

## Creating a Cubemap
A cubemap is a texture like any other texture.

```c++
unsigned int texture;
glTextures(1, &texture);
glBindTexture(GL_TEXTURE_CUBE_MAP, texture);
```

We have to call `glTexImage2D` six times, each time setting the texture target parameter to match a specific face of the cubemap.
- `GL_TEXTURE_CUBE_MAP_POSITIVE_X` = Right
- `GL_TEXTURE_CUBE_MAP_POSITIVE_Y` = Left
- `GL_TEXTURE_CUBE_MAP_NEGATIVE_X` = Top
- `GL_TEXTURE_CUBE_MAP_NEGATIVE_Y` = Bottom
- `GL_TEXTURE_CUBE_MAP_POSITIVE_Z` = Back
- `GL_TEXTURE_CUBE_MAP_POSITIVE_Y` = Front
These can be linearly incremented

```c++
int width, height, nrChannels;
unsigned char *data;
for (unsigned int i =0; i < textures_faces.size(); ++i) {
	data = stbi_load(textuers_faces[i].c_str(), &width, &height, &nrChannels, 0);
	glTexImage2D(GL_TEXTURE_CUBE_MAP_POSITIVE_X + i, 0, GL_RGB, width, height, 0, GL_RGB, GL_UNSIGNED_BYTE, data);
}
```

We also need to specify its wrappings like any other texture.
```c++
glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE);
glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);
glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_R, GL_CLAMP_TO_EDGE);
```

`GL_TEXTURE_WRAP_R` simply sets the wrapping and filtering methods for the texture's `R` coordinate which correponds to the texture's 3rd dimension (like z for positions).

We use `GL_CLAMP_TO_EDGE` since texture coordinates that are exactly between two faces may not hit an exact face. This allows OpenGL to always return their edge values whenever we sample between faces.

Then before drawing the objects that will use the cubemap, we activate the correponding texture unit and bind the cubemap before rendering.

The fragment shader needs to use the type `samplerCube` that we query with a `vec3` direction vector.

```glsl
in vec3 textureDir;
uniform samplerCube cubemap;

void main() {
	FragColor = texture(cubemap, textureDir);
}
```

