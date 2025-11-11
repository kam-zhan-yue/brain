Following the steps in 26-1, we can generate a framebuffer. We need to then render the framebuffer's buffers instead of the default framebuffers by simply binding the framebuffer object. All subsequent render commands will then influence the currently bound framebuffer.

All the depth and stencil operations will also read from the currently bound framebuffer depth and stencil attachments if they're available. If you omit a depth buffer, for example, all depth testing operations will no longer work.

To draw the scene to a single texture, we need to do the following steps:
1. Render the scene as usual with the new framebuffer bound as the active framebuffer
2. Bind to the default framebuffer
3. Draw a quad that spans the entire screen with the new framebuffer's colour buffer as its texture

```c++
// first pass

glBindFramebuffer(GL_FRAMEBUFFER, framebuffer);
glClearColor(0.1f, 0.1f, 0.1f, 1.0f);
glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
glEnable(GL_DEPTH_TEST);

DrawScene();

// second pass         glBindFramebuffer(GL_FRAMEBUFFER, 0); // back to default
glClearColor(1.0f, 1.0f, 1.0f, 1.0f);
glClear(GL_COLOR_BUFFER_BIT);

screenShader.use();
glBindVertexArray(quadVAO);
glDisable(GL_DEPTH_TEST);
glBindTexture(GL_TEXTURE_2D, textureColorbuffer);
glDrawArrays(GL_TRIANGLES, 0, 6);
```

- Since each framebuffer we're using has its own set of buffers, we want to clear each of those buffers with the appropriate bits
- When drawing the quad, we disable the depth test since we want to make sure the quad always renders in front of everything else. We enable depth testing again when we draw the normal scene
