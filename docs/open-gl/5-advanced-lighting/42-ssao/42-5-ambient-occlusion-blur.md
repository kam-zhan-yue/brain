Between the SSAO pass and the lighting pass, we want to blur the SSAO texture.

### Framebuffer

```c++
Blur generateBlur() {
  unsigned int blurFBO, blurTexture;
  glGenFramebuffers(1, &blurFBO);
  glBindFramebuffer(GL_FRAMEBUFFER, blurFBO);
  glGenTextures(1, &blurTexture);
  glBindTexture(GL_TEXTURE_2D, blurTexture);
  glTexImage2D(GL_TEXTURE_2D, 0, GL_RED, windowWidth, windowHeight, 0, GL_RED, GL_FLOAT, NULL);
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
  glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_2D, blurTexture, 0);
  if (glCheckFramebufferStatus(GL_FRAMEBUFFER) != GL_FRAMEBUFFER_COMPLETE)
    cout << "Framebuffer is not complete." << endl;

  glBindFramebuffer(GL_FRAMEBUFFER, 0);
  glBindTexture(GL_TEXTURE_2D, 0);
  return {
    .buffer = blurFBO,
    .texture = blurTexture,
  };
}
```

### Fragment Shader
Because the tiled random vector texture gives a consistent randomness, we can use this to create a simple blur shader.

```
#version 330 core

in vec2 texCoords;

uniform sampler2D ssaoInput;

out float FragColor;

void main() {
  vec2 texelSize = 1.0 / vec2(textureSize(ssaoInput, 0));
  float result = 0.0;
  for (int x=-2; x<2; ++x) {
    for (int y=-2; y<2; ++y) {
      vec2 offset = vec2(float(x), float(y)) * texelSize;
      result += texture(ssaoInput, texCoords + offset).r;
    }
  }
  FragColor = result / (4.0 * 4.0);
}
```

- Traverses the surrounding SSAO texels between -2.0 and 2.0, sampling the SSAO texture an amount identical to the noise texture's dimensions.
- Offset each texture coordinate by the exact size of a single texel using `textureSize` that returns a vec2 of the given texture's dimensions.