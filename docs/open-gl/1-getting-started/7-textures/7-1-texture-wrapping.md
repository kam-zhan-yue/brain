If we specify coordinates out of the (0, 0) to (1, 1) coordinate range, the default behaviour of OpenGL is to repeat texture images. There are other options OpenGL offers:
- `GL_REPEAT`: the default repeating behaviour
- `GL_MIRRORED_REPEAT`: Same as `GL_REPEAT` but mirrors the image with each repeat
- `GL_CLAMP_TO_EDGE`: Clamps the coordinates between 0 and 1. The result is that higher coordinates become clamped to the edge, resulting in a stretched edge pattern
- `GL_CLAMP_TO_BORDER`: Coordinates outside the range are not given a user-specified border
- 