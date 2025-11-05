We know that the z-value of the built-in `gl_FragCoord` vector in the fragment shader contains the depth value of that particular fragment. If we output this depth value, we can display the depth value for all fragments in the scene.

```glsl
void main() {
	FragColor = vec4(vec3(gl_FragCoord.z), 1.0);
}
```

However, most things are white since there is a high precision for small z-values and a low precision for high z-values. The depth value of the fragment rapidly increases over distance, so almost all the vertices have values close to 1.0. Only if we move really close to an object do you see that the colours get darker.

![[22-3-depth-buffer-visualisation.png]]

We can transform the non-linear depth values back to its linear form. To reverse the process of projection, we need to re-transform the depth values from [0, 1] to normalised device coordinates in the range [-1, 1].

Then we want to reverse the non-linear equation as done in the projection matrix and apply this inversed equation to the resulting depth value.

```glsl
float linearizeDepth(float depth) {
  float ndc = 2.0 * depth - 1.0;
  return (2.0 * near * far) / (far + near - z * (far - near));
}

void main() {
  float linearDepth = linearizeDepth(gl_FragCoord.z) / far;
  FragColor = vec4(vec3(linearDepth), 1.0);
}
```

We divide the linear depth value by `far` to convert the linear depth value to the range [0, 1].