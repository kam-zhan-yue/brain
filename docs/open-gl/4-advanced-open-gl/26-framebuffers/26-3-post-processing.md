Now that the entire scene is rendered to a single texture, we can create post-processing effects by manipulating the scene texture.

### Inversion
We can invert the colours of the render output in the fragment shader.

```glsl
void main() {
	FragColor = vec4(vec3(1.0 - texture(screenTexture, TexCoords), 1.0)
}
```

We can also remove all colours expect for white, gray, and black.

```c++
void main() {
	vec4 textureColour = texture(screenTexture, TexCoords);
	float average = (textureColour.r + textureColour.g + textureColour.b) / 3.0;
	FragColor = vec4(vec3(average), 1.0);
}
```