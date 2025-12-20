## Gaussian Curve

An easy blur would be to take the average of all surrounding pixels of an image. A Gaussian blur is based on the Gaussian curve, which is known as a bell-curve, giving high values close to its centre that gradually wear off over distance.

As the Gaussian curve has a larger area close to its centre, using its values as weights to blur an image gives more natural results as samples close by have a higher precedence. If we sample a 32x32 box around a fragment, we progressively use smaller weights the larger the distance to the fragment, giving a better and more realistic blur known as a Gaussian blur.

To implement a Gaussian blur, we need a two-dimensional box of weights that we can obtain from a 2-dimensional Gaussian curve equation. However, this quickly becomes heavy on performance as each blur kernel would be needed on each fragment. sampling a texture 1024 times per fragment.

## Two Pass Gaussian Blur
The Gaussian equation has a very neat property that allows us to separate the two-dimensional equation into two smaller one-dimensional equations: one that describes the horizontal weights and the other that describes the vertical weights.
- We first do a horizontal blur with the horizontal weights on the scene texture
- Then on the resulting texture, do a vertical blur
- Due to this property, the results are exactly the same, but this time saving us performance as we'd have to do 32 + 32 samples compared to 32 * 32
- This is known as two-pass Gaussian blur

![[40-2-two-pass-blur.png]]

## Ping-Pong Framebuffers
This means we need to blur an image at least two times and this works with the use of framebuffer objects. For the two-pass blur, we can implement ping-pong framebuffers.

This is a pair of framebuffers where we render and swap, a given number of times, the other framebuffer's colour buffer into the current framebuffer's colour buffer with an alternating shader effect. We basically continuously switch the framebuffer to render to and the texture to draw with. This allows us to first blur the scene's texture in the first framebufer, then blur the first framebuffer's colour buffer into the second framebuffer, and then the second framebuffer's colour buffer into the first, and so on.

> This lets us chain effects together without having to use several framebuffers.

## Gaussian Blur Fragment Shader

```
#version 330 core

out vec4 FragColor;
in vec2 texCoords;

uniform sampler2D image;
uniform bool horizontal;
// weights get less important the further it goes
uniform float weight[5] = float[] (0.2270270270, 0.1945945946, 0.1216216216, 0.0540540541, 0.0162162162);

void main() {
  // size of a single texel
  vec2 texOffset = 1.0 / textureSize(image, 0);
  // this fragment
  vec3 result = texture(image, texCoords).rgb * weight[0];
  if (horizontal) {
    for (int i=0; i<5; ++i) {
      result += texture(image, texCoords + vec2(texOffset.x * i, 0.0)).rgb * weights[i];
      result += texture(image, texCoords - vec2(texOffset.x * i, 0.0)).rgb * weights[i];
    }
  } else {
    for (int i=0; i<5; ++i) {
      result += texture(image, texCoords + vec2(texOffset.y * i, 0.0)).rgb * weights[i];
      result += texture(image, texCoords - vec2(texOffset.y * i, 0.0)).rgb * weights[i];
    }
  }
  FragColor = vec4(result, 1.0);
}
```

We take a relatively small sample of Gaussian weights that we use to assign a specific weight to the horizontal or vertical samples around the current fragment. We split the blur filter into a horizontal and vertical section and base the size of a texel from the division over the size of a texture.

## Ping Pong Framebuffer Code

We create two basic framebuffers, each with a colour buffer texture.

```c++
  // Ping Pong Framebuffer Setup
  unsigned int pingpongFBO[2];
  unsigned int pingpongTextures[2];
  glGenFramebuffers(2, pingpongFBO);
  glGenTextures(2, pingpongTextures);
  for (unsigned int i=0; i<2; i++) {
    glBindFramebuffer(GL_FRAMEBUFFER, pingpongFBO[i]);
    glBindTexture(GL_TEXTURE_2D, pingpongTextures[i]);
    glTexImage2D(GL_TEXTURE_2D, 0, GL_RGBA16F, SCREEN_WIDTH, SCREEN_HEIGHT, 0, GL_RGBA, GL_FLOAT, NULL);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);
    glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_2D, pingpongTextures[i], 0);
  }
```

After we've obtained the HDR texture and an extracted brightness texture, we first ill one of the ping-pong framebuffers with the brightness texture and then blur the image 10 times (5 times vertically and 5 times horizontally).

```c++
    bool horizontal = true, first_iteration = true;
    int amount = 10;
    scene.blur.shader.use();
    for (unsigned int i=0; i<amount; i++) {
      glBindFramebuffer(GL_FRAMEBUFFER, scene.buffers.pingpongFBO[horizontal]);
      scene.blur.shader.setBool("horizontal", horizontal);
      glBindTexture(GL_TEXTURE_2D, first_iteration ? scene.buffers.colorBuffers[1] : scene.buffers.pingpongTextures[!horizontal]);
      renderQuad(scene.quad);
      horizontal = !horizontal;
      first_iteration = false;
    }
```

- Each iteration, we bind one of the two framebuffers based on whether we want to blur horizontally or vertically and bind the other framebuffer's colour buffer as the texture to blur.
- The first iteration we specifically bind the brightness colour buffer
- The second iteration onwards uses the ping pong buffers
- Repeating the process 10 times ends up with a completed Gaussian blur that was repeated 5 times.
- The more Gaussian blur iterations, the stronger the blur

By blurring the extracted brightness texture 5 times, we get a properly blurred image of all bright regions in a scene.
