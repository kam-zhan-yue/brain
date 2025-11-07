There are two functions we can use to configure stencil testing.

## `glStencilFunc`

Takes three parameters
- `GLenum func`: sets the stencil test function that determines whether a fragment passes or is discarded. Possible objects are `GL_NEVER`, `GL_LESS`, etc etc
- `GLint ref`: specifies the reference value for the stencil test. The stencil buffer's content is compared to this value.
- `GLuint mask`: specifies a mask that is `AND`ed with both the reference value and the stored stencil value before the test compares them. Initially set to 1s.

In the case of the simple stencil example, the function would be set to:
```c++
glStencilFunc(GL_EQUAL, 1, 0xFF);
```

## `glStencilOp`

Takes three parameters
- `GLenum sfail`: action to take if the stencil test fails
- `GLenum dpfail`: action to take if the stencil pass passes, but the depth test fails
- `GLenum dppass`: action to take if both the stencil and depth test pass

For each of the options, we can do the following:

| Action       | Description                                                                       |
| ------------ | --------------------------------------------------------------------------------- |
| GL_KEEP<br>  | The currently stored stencil value is kept.                                       |
| GL_ZERO      | The stencil value is set to 0.                                                    |
| GL_REPLACE   | The stencil value is replaced with the reference value set with glStencilFunc.    |
| GL_INCR      | The stencil value is increased by 1 if it is lower than the maximum value.        |
| GL_INCR_WRAP | Same as GL_INCR, but wraps it back to 0 as soon as the maximum value is exceeded. |
| GL_DECR      | The stencil value is decreased by 1 if it is higher than the minimum value.       |
| GL_DECR_WRAP | Same as GL_DECR, but wraps it to the maximum value if it ends up lower than 0.    |
| GL_INVERT    | Bitwise inverts the current stencil buffer value.                                 |
By default, the `glStencilOp` function is set to `(GL_KEEP, GL_KEEP, GL_KEEP)`.

By using these two functions, we can precisely specify when and how we want to update the stencil buffer and when to pass or discard fragments based on its content.