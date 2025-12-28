We can create a random rotation vector for each fragment of a scene, but that quickly eats memory. It makes more sense to create a small texture of random rotation vectors that we tile over the screen.

We create a 4x4 array of random rotation vectors oriented around the tangent-space surface normal.

```c++
  vector<glm::vec3> noise;
  for (unsigned int i=0; i<16; ++i) {
    glm::vec3 sample(
      randomFloats(generator) * 2.0 - 1.0,
      randomFloats(generator) * 2.0 - 1.0,
      0.0,
    );
    noise.push_back(sample);
  }
```

- As the sample kernel is oriented along the positive z direction, we leave the z component at 0.0 so that we can rotate around the z-axis
- Then we create a 4x4 texture that holds the random rotation vectors; making sure to set its wrapping method to GL_REPEAT so that it properly tiles over the screen.

```c++
// Noise texture
unsigned int noiseTexture;
glGenTextures(1, &noiseTexture);
glBindTexture(GL_TEXTURE_2D, noiseTexture);
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGBA16F, 4, 4, 0, GL_RGB, GL_FLOAT, &noises[0]);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);
```
