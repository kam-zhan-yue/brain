Object outlining can be achieved with the stencil buffer by creating a small coloured border around the object(s). The routine for outlining objects is:
1. Enable stencil writing
2. Set the stencil op to `GL_ALWAYS` before drawing the objects, updating the stencil buffers with 1s wherever the objects' fragments are rendered
3. Render the objects
4. Disable stencil writing and depth testing
5. Scale each of the objects by a small amount
6. Use a different fragment shader that outputs a single border colour
7. Draw the objects again, but only if their fragments' stencil values are not equal to 1
8. Enable depth testing again and restore stencil value to `GL_KEEP`

This process sets the content of the stencil buffer to 1s for each of the object's fragments and when it's time to draw the borders, we draw scaled-up versions of the objects only where the stencil test passes.

We effectively discard all the fragments of the scaled-up versions that are part of the original objects' fragments during the stencil buffer.

We make a shader to output a single colour.

```glsl
void main() {
	FragColor = vec4(0.04, 0.28, 0.26, 1.0);
}
```

Then, enable stencil testing

```c++
glEnable(GL_STENCIL_TEST);
```

In each frame, specify the action to take whenever the stencil tests succeed or fail.

```c++
glStencilOp(GL_KEEP, GL_KEEP, GL_REPLACE);
```

Only if both the stencil and depth test succeed that we want to replace the stored stencil value with the reference value set via `glStencilFunc`.

Clear the stencil buffer to 0s at the start of the frame and for the containers, we update the stencil buffer to 1 for each fragment drawn:

```c++
glStencilOp(GL_KEEP, GL_KEEP, GL_REPLACE);
glStencilFunc(GL_ALWAYS, 1, 0xff); // all fragments pass
glStencilMask(0xFF); // enable writing to the buffer
normalShader.use();
DrawTwoContainers();
```

By using `GL_REPLACE` as the stencil op function, we make sure that each of the containers' fragments update the stencil buffer with a stencil value of 1. Because the fragments always pass the stencil test, the stencil buffer is updated with the reference value whenever we've drawn them.

Now, the stencil buffer is updated with 1s where the containers were drawn. We can draw the upscaled containers.
```c++
glStencilFun(GL_NOTEQUAL, 1, 0xFF); // pass if not equal to 1
glStencilMask(0x00); // disable writing
glDisable(GL_DEPTH_TEST);
outlineShader.use();
DrawTwoScaledUpContainers();
```

We set the stencil function to `GL_NOTEQUAL` to make sure that we're only drawing parts of the containers that are not set to 1, hence drawing only the parts of the containers that are outside the previously drawn containers.

We also disable depth testing so that the scaled up containers do not get overwritten by the floor.