Because GLFW takes care of creating the multisampled buffers, enabling MSAA is quite easy. If we want to use our own framebuffers, we have to generate the multisampled buffers ourselves.

There are two ways to create multisampled buffers to act as attachments for framebuffers: texture attachments and renderbuffer attachments.

## Multisampled Texture Attachments

To create a texture that supports storage of multiple sample points, we use `glTexImage2DMultisample` instead of `glTexImage2D` that accepts `GL_TEXTURE_2D_MUTLISAMPLE` as its texture target.

```c++
glBindTexture(GL_TEXTURE_2D_MULTISAMPLE, tex);
glTexImage2DMultisample(GL_TEXTURE_2D_MUTLISAMPLE, samples, GL_RGB, width, height, GL_TRUE);
glBindTexture(GL_TEXTURE_2D_MULTISAMPLE, 0);
```

If the last argument is set to `GL_TRUE`, the image will use identical sample locations and the same number of subsamples for each texel.

To attach a multisampled texture to a framebuffer, we use `glFramebufferTexture2D`, but with `GL_TEXTURE_2D_MUTLISAMPLE`.

```c++
glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_2D_MULTISAMPLE, tex, 0);
```

## Mutlisampled Renderbuffer Objects
Like textures, creating a multisample renderbuffer object isn't difficult.

```c++
glRenderbufferStorageMultisample(GL_RENDERBUFFER, 4, GL_DEPTH24_STENCIL8, width, height);
```

We set the second parameter to the amount of samples we'd like to use.

## Rendering to a Multisampled Framebuffer

Whenever we draw anything while the framebuffer is bound, the rasteriser will take care of all multisample operations. However, because a multisampled buffer is a bit special, we can't use the buffer for other operations like sampling it in a shader.

A multisampled image contains much more information than a normal image, so we need to downscale or resolve the image. Resolving a multisampled framebuffer is generally done through `glBlitFramebuffer` that copies a region form one framebuffer to the other while also resolving any multisampled buffers.

`glBlitFramebuffer` transfers a given **source** region defined by 4 screen-space coordinates to a given target region also defined by 4 screen-space coordinates. The `glBlitFramebuffer` reads from the read and draw framebuffer targets to determine which is the source and which is the target framebuffer. We can then transfer the multisampled framebuffer output to the screne by blitting the image to the default framebuffer.

```c++
glBindFramebuffer(GL_READ_FRAMEBUFFER, multisampledFBO);
glBindFramebuffer(GL_DRAW_FRAMEBUFFER, 0);
glBlitFramebuffer(0, 0, width, height, 0, 0, width, height, GL_COLOR_BUFFER_BIT, GL_NEAREST);
```

However, if we wanted to use the texture result of a multisampled framebuffer to do things like ost-processing, we need to blit the multisampled buffer to a different FBO with a non-multisampled texture attachment. We then use this ordinary colour attachment texture for post-processing. We effectively post-process an image rendered via multisampling.