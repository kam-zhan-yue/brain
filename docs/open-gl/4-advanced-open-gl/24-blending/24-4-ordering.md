To make blending work for multiple objects, we have to draw the most distant object first and the closest object last. The normal non-blended objects can still be drawn as normal using the depth buffer so they don't have to be sorted.

When drawing a scene with non-transparent and transparent objects, the general outline is:

1. Draw all opaque objects first
2. Sort all the transparent objects
3. Draw all the transparent objects in sorted order

We can store this in a `map` because a `map` automatically sorts its values based on its keys. Once we've added all positions with their distance as their key, they're automatically sorted on distance value.

```c++
std::map<float, glm::vec3> sorted;
for (unsigned int i=0; i<windows.size(); ++i) {
	float distance = glm::length(camera.Position - windows[i]);
	sorted[distance] = windows[i];
}
```

Then when rendering, we take the map's values in reverse other (from farthest to nearest).

```c++
for std::map<float, glm::vec3>::reverse_iterator it = sorted.rbegin(); it != sorted.rend(); ++it) {
	model = glm::mat4(1.0f);
	model = glm::translate(model, it->second);
	shader.setMat4("model", model);
	glDrawArrays(GL_TRIANGLES, 0, 6);
}
```

This only takes position into account and not rotation, etc. Completely rendering a scene with solid and transparent objects requires advanced techniques such as order independent transparency.