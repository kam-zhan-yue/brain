Another advantage of post-processing on a single texture image is that we can sample colour values from other parts of the texture not specific to that fragment.

For example, we can take a small area around the current texture coordinate and sample multiple texture values around the current texture value.

## Kernels

A kernel (or convolution matrix) is a small matrix-like array of values centred on the current pixel that multiplies surrounding pixel values by its kernel values and adds them all together to form a single value. An example kernel is as follows:

$$
\left[
\begin{array}{cc}
2 & 2 & 2 \\
2 & -15 & 2 \\
2 & 2 & 2 \\
\end{array}
\right]
$$

The kernel takes 8 surrounding pixel values and multiplies them y 2 and the current pixel by -15. Most kernels will sum up to 1 if you add all the weights together. If they don't sum to 1, it means that the resulting texture colour ends up brighter or darker than the original texture value.

We have to adapt the fragment shader to support kernels. We make the assumption that each kernel is 3x3.

```glsl
const float offset = 1.0 / 300.0;

void main() {
  vec2 offsets[9] = vec2[](
    vec2(-offset, offset),    // top left
    vec2(0.0f, offset),       // top center
    vec2(offset, offset),     // top right
    vec2(-offset, 0.0f),      // center left
    vec2(0.0f, 0.0f),         // center center
    vec2(offset, 0.0f),       // center right
    vec2(-offset, -offset),   // bottom left
    vec2(0.0f, -offset),      // bottom center
    vec2(offset, -offset)    // bottom right
  );

  float kernel[9] = float[](
    -1, -1, -1,
    -1, 9, -1,
    -1, -1, -1
  );

  vec3 sampleTex[9];
  for (int i=0; i<9; ++i) {
    sampleTex[i] = vec3(texture(screenTexture, TexCoords.st + offsets[i]));
  }
  vec3 col = vec3(0.0);
  for (int i=0; i<9; ++i) {
    col += sampleTex[i] * kernel[i];
  }
  FragColor = vec4(col, 1.0);
}
```

In the shader, we create an array of 9 vec2 offsets for each surrounding texture coordinate. The offset is a constant value that can be customised. Then we define the kernel, which is a sharpen kernel that sharpens each colour value by sampling all surrounding pixels in an interesting way. Lastly, we add an offset to the current texture coordinate when sampling and multiply these texture values with the weighted kernel values.

### Blur
We can create blur with this kernel
$$
\left[
\begin{array}{cc}
1 & 2 & 1 \\
2 & 4 & 2 \\
1 & 2 & 1 \\
\end{array}
\right]
 / 16
$$
Because all values add up to 16, directly returning the combined sampled colours would result in an extremely bright colour, so we divide each value of the kernel by 16.

We can vary the blur amount over time to make the effect of someone being drunk, or increase the blur when the character is not wearing glasses.

Blurring is also useful for smoothing colour values.

### Edge Detection
An edge-detection kernel is similar to the sharpen kernel.
$$
\left[
\begin{array}{cc}
1 & 1 & 1 \\
1 & -8 & 1 \\
1 & 1 & 1 \\
\end{array}
\right]
$$
The kernel highlights all edges and darkens the rest. Kernels are used for image-manipulating tools / filters in tools like photoshop.