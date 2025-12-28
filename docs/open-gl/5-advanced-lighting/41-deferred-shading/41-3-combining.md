If we want to render each light source as a 3D cube positioned at the light source's position emitting the colour of light, we might forward render all the light sources on top of the deferred lighting quad at the end of the deferred shading pipeline. 

```c++
renderQuad();
renderLights();
```

However, these rendered cubes do not take any of the stored geometry depth of the deferred renderer into account and will always render on top of the previously rendered objects.

We need to first copy the depth information stored in the geometry pass into the default framebuffer's depth buffer, and only then render the light cubes. This way the light cube's fragments are only rendered when on top of the previously rendered geometry.

We can copy the content of a framebuffer to the content of another framebuffer with the help of `glBlitFramebuffer`, which allows us to copy a user-defined region of a framebuffer to a user-defined region of another framebuffer.

## Blitting
We stored the depth of all the objects rendered in the `gBuffer` FBO. If we were to copy the content of its depth buffer to the depth buffer of the default framebuffer, the light cubes would then render as if all of the scene's geometry was rendered with forward rendering.

We need to specify a framebuffer as the read framebuffer and one as the write framebuffer.

```c++
void forwardRendering(Scene scene) {
  glBindFramebuffer(GL_READ_FRAMEBUFFER, scene.buffers.gBuffer);
  glBindFramebuffer(GL_DRAW_FRAMEBUFFER, 0);
  glBlitFramebuffer(0, 0, windowWidth, windowHeight, 0, 0, windowWidth, windowHeight, GL_DEPTH_BUFFER_BIT, GL_NEAREST);
  glBindFramebuffer(GL_FRAMEBUFFER, 0);
  // Forward Rendering
  renderLights(scene);
}
```

With this, we can combine deferred shading with forward shading.