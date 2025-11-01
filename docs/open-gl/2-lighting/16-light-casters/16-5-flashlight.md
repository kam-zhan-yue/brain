A flashlight is a spotlight located at the viewer's position and usually aimed straight ahead from the player's perspective. A flashlight is basically a normal spotlight, but with its position and directions updated based on the player's position and orientation.

We can store these in the light:

```glsl
struct Light {
	vec3 position;
	vec3 direction;
	float cutOff;
}
```

And set these in OpenGL
```c++
shader.setVec3("light.position", camera.Position);
shader.setVec3("light.direction", camera.Front);
shader.setFloat("cutOff", glm::cos(glm::radians(12.5f)));
```

We don't set an angle for the cutoff value, but we calculate the cosine value based on an angle and pass the cosine result to the fragment shader. The reason for this is that in the fragment shader, we're calculating the dot product and the dot product returns a cosine value and not an angle; and we can't directly compare an angle with a cosine value. To get the angle in the shader, we have to calculate the inverse cosine, which is an expensive operation on every fragment.

Then, we calculate the theta value and compare this with the cutoff to determine if we're in or outside the spotlight.

> We use the > sign instead of the < sign in the `if` guard as the closer the cosine value is to 1.0, the smaller the angle. The cutoff value is at cosine 12.5, which is equal to 0.976, so a cosine theta value between 0.976 and 1.0 would be "in the spotlight"