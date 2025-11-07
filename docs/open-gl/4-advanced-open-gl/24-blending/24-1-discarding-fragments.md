Some effects don't use transparency, but either want to show something or not. E.g. grass is implemented by pasting a grass texture onto a 2D quad. However, since grass isn't shaped like a 2D square, you only want to display some parts of the grass texture.

When adding vegetation, we don't want to see a square image of grass, but rather only show the actual grass and see through the rest of the image. We want to discard the fragments that show the transparent parts of the texture, not storing that fragment into the colour buffer. But we also want to show what's behind it.

To load a transparent texture, we can do
```c++
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGBA, width, height, 0, GL_RGBA, GL_UNSIGNED_BYTE, data);
```

We can then render the grass texture with a shader that discards fragments if they are low in transparency.

```glsl
void main() {
	void 4 texColour = texture(texture1, TexCoords);
	if (texColour.a < 0.1) {
		discard;
	}
	FragColor = texColour;
}
```


### On Borders
When sampling textures at their borders, OpenGL interpolates the border values with the next repeated value of the texture (on GL_REPEAT). This is usually okay, but with transparent values, the top of the image texture gets its transparent value interpolated with the bottom border's solid colour value. The result is then a semi-transparent coloured border you may see wrapped around the quad. To prevent this, we need to set the texture wrapping method to `GL_CLAMP_TO_EDGE`.
```c++
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);
```

