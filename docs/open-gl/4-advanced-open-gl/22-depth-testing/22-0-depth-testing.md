The depth buffer is a buffer that, just like the colour buffer (that stores all the fragment colours: the visual output), stores information per fragment and has the same width and height as the colour buffer. The depth buffer is automatically created by the windowing system and stores its depth values as 16, 24, or 32 bit floats. In most systems, the depth buffer will have a precision of 24 bits.

When depth testing is enabled, OpenGL tests the depth value of a fragment against the content of the depth buffer. OpenGL performs a depth test and if this test passes, the fragment is rendered and the depth buffer is updated with the new depth value. If the depth test fails, the fragment is discarded.

Depth testing is done in screen space after the fragment shader has run (and after the stencil test). The screen space coordinates relate directly to the viewport defined by `glViewport` and can be accessed by `gl_FragCoord`. The x and y components of `gl_FragCoord` represent the fragment's screen-space coordinates. The `gl_FragCoord` variable also contains a z-component which stores the depth value of the fragment. This z value is the value that is compared to the depth buffer's content.

### Early Depth Testing
Most GPU's have a hardware feature called early depth testing. This allows the depth test to run before the fragment shader runs. Whenever it is clear that a fragment isn't going to be visible, we can prematurely discard the fragment. A restriction on the fragment shader for early depth testing is that you shouldn't write to the fragment's depth value. If so, early depth testing is impossible as OpenGL won't be able to figure out the depth value beforehand.

Depth testing is disabled by default so to enable depth testing, we enable it with

```c++
glEanble(GL_DEPTH_TEST);
```

Once enabled, OpenGL automatically stores the z-values of fragments in the depth buffer. If they pass the depth test, the fragments are discarded. We must also clear the depth buffer each frame.

```c++
glClear(GL_COLOUR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
```

There are certain scenarios where you want to perform the depth test on all fragments and discard them, but not update the depth buffer. This would be a **read-only** depth buffer. OpenGL allows us to disable writing to the depth buffer by setting its depth mask to `GL_FALSE`.

```c++
glDepthMask(GL_FALSE);
```