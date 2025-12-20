Bloom refers to the effect of light bleeding around the light source in order to give the illusion that the light sources or bright regions are intensely bright. Bloom gives noticeable visual cues about the brightness of objects and significantly boosts the lighting of a scene and allows for a large range of dramatic effects.

Bloom works best in combination with HDR rendering. A common misconception is that HDR is the same as bloom. It is possible to implement Bloom with default 8-bit precision framebuffers, just as it is possible to use HDR without the bloom effect. It is simply that HDR makes Bloom more effective to implement.

To implement bloom, we render a lit scene as usual and extract both the scene's HDR colour buffer and an image of the scene with only its bright regions visible. This extracted brightness image is then blurred and the result is added on top of the original HDR scene image
## Example
Take this image for example.

![[40-1-bloom-1.png]]

We take the HDR colour buffer texture and extract all the fragments that exceed a certain brightness. This gives us an image that only shows the bright coloured regions as their fragment intensities exceeded a certain threshold.

![[40-1-bloom-2.png]]

Then, we blur the result. The strength of the bloom effect is largely determined by the range and strength of the blur filter used.

![[40-1-bloom-3.png]]

The resulting blurred texture is what is used to get the glow or light-bleeding effect by adding it on top of the original HDR scene. Because the bright regions are extended in both width and height due to the blur filter, the bright regions appear to glow or bleed light.

![[40-1-bloom-4.png]]

## Extracting Bright Colour

The first step is to extract two images from a rendered scene. This is possible by rendering to different framebuffers, but we can use *Multiple Render Targets (MRT)* that allows us to specify more than one fragment shader output. This gives us the option to extract the first two images in a single render pass. 

By specifying a layout location specifier before a fragment shader's output, we can control to which colour buffer a fragment shader writes to.

```
layout (location = 0) out vec4 FragColor;
layout (location = 1) out vec4 BrightColor;
```

This only works if we have multiple buffers to write to. As a requirement for using multiple fragment shader outputs, we need multiple colour buffers attached to the currently bound framebuffer object.

```
  unsigned int FBO;
  glGenFramebuffers(1, &FBO);
  glBindFramebuffer(GL_FRAMEBUFFER, FBO);

  unsigned int colorBuffers[2];
  glGenTextures(2, colorBuffers);
  for (unsigned int i=0; i<2; i++) {
    glBindTexture(GL_TEXTURE_2D, colorBuffers[i]);
    glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB16F, SCREEN_WIDTH, SCREEN_HEIGHT, 0, GL_RGBA, GL_FLOAT, NULL);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);
    glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0 + i, GL_TEXTURE_2D, colorBuffers[i], 0);
  }
```

We have to explicitly tell OpenGL to render to multiple colour buffers via glDrawBuffers. By default, OpenGL only renders to a framebuffer's first colour attachment, ignoring all others. 

```
  unsigned int RBO;
  glGenRenderbuffers(1, &RBO);
  glBindRenderbuffer(GL_RENDERBUFFER, RBO);
  glRenderbufferStorage(GL_RENDERBUFFER, GL_DEPTH_COMPONENT, SCREEN_WIDTH, SCREEN_HEIGHT);
  glFramebufferRenderbuffer(GL_FRAMEBUFFER, GL_DEPTH_ATTACHMENT, GL_RENDERBUFFER, RBO);

  // Explicitly tell OpenGL to use two colour attachments
  unsigned int attachments[2] = { GL_COLOR_ATTACHMENT0, GL_COLOR_ATTACHMENT1 };
  glDrawBuffers(2, attachments);
```

When rendering to the framebuffer, whenever a fragment shader uses the layout location specifier, the respective colour buffer is used to render the fragment to. This is great as it saves us an extra render pass for extracting bright regions as we can now directly extract them from the to-be-rendered fragment.

```
  vec3 lighting = ambient + diffuse;
  FragColor = vec4(lighting, 1.0);

  // if the fragment output is brighter than the threshold, then output the brightness colour
  float brightness = dot(FragColor.rgb, vec3(0.2126, 0.7152, 0.0722));
  BrightColor = brightness > 1.0 ? vec4(FragColor.rgb, 1.0) : vec4(vec3(0.0), 1.0);
```

- Calculate the lighting as normal and pass it to the first fragment shader's output variable, FragColor. 
- Calculate the brightness of a fragment by properly transforming it to grayscale first (by taking the dot product of both vectors, we effectively multiple each individual component of both vectors and add the results together)
- If the brightness exceeds a certain threshold, we output the colour to the second colour buffer.

This is also why bloom works well with HDR rendering. Because we render in HDR, our values can exceed 1.0, which allows us to specify a brightness threshold outside the default range, giving us more control over what is considered bright.

Without HDR, we'd have to set the threshold lower than 1.0 which is still possible, but regions are much quickly considered bright, leading the glow effect becoming too dominant.