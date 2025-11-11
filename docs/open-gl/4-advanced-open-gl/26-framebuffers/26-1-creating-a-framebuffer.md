The framebuffer is a combination of the colour buffer, depth buffer, and stencil buffer. OpenGL gives us the flexibility to define our own framebuffers and define our own colour buffer.

The rendering operations were done on the default framebuffer. This is created and configured when the window is created. By creating our own framebuffer, we can get an additional target to render to.

We can create a framebuffer like so:

```c++
unsigned int fbo;
glGenFramebuffers(1, &fbo);
```

Then we bind, so some operations, then unbind.

```c++
glBindFramebuffer(GL_FRAMEBUFFER, fbo);
```

All the next read and write framebuffer operations will affect the currently bound framebuffer. It is also possible to bind a framebuffer to a read of write target with `GL_READ_FRAMEBUFFER` or `GL_DRAW_FRAMEBUFFER`

For a framebuffer to be complete, we need to satisfy the following requirements:
- We have to attach at least one buffer (colour, depth, or stencil)
- There should be at least one colour attachment
- All attachments should be complete as well (reserved memory)
- Each buffer should have the same number of samples

We can check if the framebuffer is complete with

```c++
if (glCheckFramebufferStatus(GL_FRAMEBUFFER) == GL_FRAMEBUFFER_COMPLETE)
```

Since our framebuffer is not the default framebuffer, the rendering commands will have no visual output on the window. This is called off-screen rendering when rendering to a different framebuffer.

If you want all rendering operations to have a visual impact again on the main window, we need to make the default framebuffer active by binding to 0:

```c++
glBindFramebuffer(GL_FRAMEBUFFER, 0);
```

When we're done with all framebuffer operations, we need to delete the object.

```c++
glDeleteFramebuffers(1, &fbo);
```

## Texture Attachments
An attachment is a memory allocation that can act as a buffer for the framebuffer, like an image. When creating an attachment, we have two options to take: texture or renderbuffer objects,

When attaching a texture to a framebuffer, all rendering commands will write to the texture as if it was a normal colour/depth or stencil buffer. The advantage of using textures is that the render output is stored inside the texture image that we can then easily use in our shaders.

```c++
unsigned int texture;
glGenTextures(1, &texture);
glBindTexture(GL_TEXTURE_2D, texture);
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, 800, 600, 0, GL_RGB, GL_UNSIGNED_BYTE, NULL);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAX_FILTER, GL_LINEAR);
```
- The main difference is that we set the dimensions equal to the screen size
- We also pass NULL as the texture's `data` parameter.
- We're only allocating memory and not actually filling it
- Filling in the texture happens as we render to the framebuffer.

After creating a texture, we need to attach it to the framebuffer.

```c++
glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_2D, texture, 0);
```
- target: the framebuffer type we're targeting
- attachment: the type of attachment we're going to attach
- textarget: the type of texture you want to attach
- texture: the actual texture to attach
- level: the mimap level

Next to the colour attachments, we can also attacha depth and stencil texture to the framebuffer. To attach a depth attachment, we specify the attachment type as `GL_DEPTH_ATTACHMENT`. The texture's format and internalformat should become `GL_DEPTH_COMPONENT` to reflect the depth buffer's storage format. To attach a stencil buffer, you use `GL_STENCIL_ATTACHMENT` and specify the texture's format as `GL_STENCIL_INDEX`.

It is also possible to attach both a depth buffer and a stencil buffer as a single texture. Each 32 bit value of the texture then contains 24 bits of depth information and 8 bits of stencil information. To attach a depth and stencil buffer as one texture, we use `GL_DEPTH_STENCIL_ATTACHMENT`.

```c++
glTexImage2D(GL_TEXTURE_2D, 0, GL_DEPTH24_STENCIL8, 800, 600, 0, GL_DEPTH_STENCIL, GL_UNSIGNED_INT_24_8, NULL);
glFraebufferTExture2D(GL_FRAMEBUFFER, GL_DEPTH_STENCIL_ATTACHMENT, GL_TEXTURE_2D, texture, 0);
```

## Renderbuffer Attachments

Renderbuffer objects are another type of framebuffer attachment. A renderbuffer is an actual buffer (an array of bytes), but it cannot be directly read from. This gives it the added advantage that OpenGL can do a few memory optimisations that can give it a performance edge over textures for off-screen rendering to a framebuffer.

Renderbuffer objects store all the render data directly into their buffer without any conversions to texture-specific formats, making them faster as a writeable storage medium. You cannot read from them directly, but it is possible to read from them via the slow `glReadPixels`. This returns a specified area of pixels from the currently bound framebuffer, but not directly from the attachment itself.

Because their data is in a native format, they are quite fast when writing data or copying data to other buffers. Operations like switching buffers are therefore fast when using renderbuffer objects. The `glfwSwapBuffers` function we've been using at the end of each frame may as well be implemented with renderbuffer objects: we simply write to a renderbuffer image, and swap to the other one at the end.

```c++
unsigned int rbo;
glGenRenderbufers(1, &rbo);
glBindRenderbuffer(GL_RENDERBUFFER, rbo);
```

Since rbos are write-only, they are often used as depth and stencil attachments since most of the time we don't really need to read values from them, but we do care about depth and stencil testing. We need the depth and stencil values for testing, but don't need to *sample* these values.

Creating a depth and stencil renderbuffer object is as follows:
```c++
glRenderbufferStorage(GL_RENDERBUFFER, GL_DEPTH24_STENCIL8, 800, 600);
```

Renderbuffer objects are specifically designed to be used as a framebuffer attachment, instead of a general purpose data buffer like a texture.

Then, we need to attach the render buffer object:
```c++
glFramebufferRenderbuffer(GL_FRAMEBUFFER, GL_DEPTH_STENCIL_ATTACHMENT, GL_RENDERBUFFER, rbo);
```

## Renderbuffers vs. Textures
The general rule is that if you never need to sample data from a specific buffer, it is wise to use a renderbuffer object for that specific buffer. If you need sample data from a specific buffer like colour or depth values, you should use a texture attachment instead.

