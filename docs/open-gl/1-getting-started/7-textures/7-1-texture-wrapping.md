If we specify coordinates out of the (0, 0) to (1, 1) coordinate range, the default behaviour of OpenGL is to repeat texture images. There are other options OpenGL offers:
- `GL_REPEAT`: the default repeating behaviour
- `GL_MIRRORED_REPEAT`: Same as `GL_REPEAT` but mirrors the image with each repeat
- `GL_CLAMP_TO_EDGE`: Clamps the coordinates between 0 and 1. The result is that higher coordinates become clamped to the edge, resulting in a stretched edge pattern
- `GL_CLAMP_TO_BORDER`: Coordinates outside the range are not given a user-specified border

![[7-1-texture.png]]

Each of the aforementioned options can be set per coordinate axis with the `glTexParameter*` function.

```c++
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_MIRRORED_REPEAT);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_MIRRORED_REPEAT);
```

- The first argument specifies the texture target. We're working with 2D textures, so the texture target is `GL_TEXTURE_2D`
- The second argument requires us to tell what option we want to set and for which texture axis. We want to configure it for the `S` and `T` axis.
- The third argument requires us to pass in the texture wrapping mode we'd like and in this case OpenGL will set its texture wrapping option on the currently active texture with `GL_MIRRORED_REPEAT`.

If we choose the `GL_CLAMP_TO_BORDER` option, we should also specify a border colour. This is done using the `fv` equivalent of the `glTexParameter` with `GL_TEXTURE_BORDER_COLOR` as its option where we pass in a float array of the border's colour value.

```c++
float borderColour[] = { 1.0f, 1.0f, 0.0f, 1.0f };
glTexParameterfv(GL_TEXTURE_2D, GL_TEXTURE_BORDER_COLOR, borderColor);
```
