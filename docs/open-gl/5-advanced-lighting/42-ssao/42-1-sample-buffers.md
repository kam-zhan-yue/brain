SSAO requires geometrical info as we need some way to determine the occlusion factor for a fragment. For each fragment, we need the following data:
- A per-fragment position vector
- A per-fragment normal vector
- A per-fragment albedo colour
- A sample kernel
- A per-fragment random rotation vector used to rotate the sample kernel.

Using a per-fragment view-space position, we can orient a sample hemisphere kernel around the fragment's view-space surface normal and use this kernel to sample the position buffer texture at varying offsets. For each per-fragment kernel sample, we compare its depth with its depth in the position buffer to determine the amount of occlusion. the resulting occlusion factor is then used to limit the final ambient lighting component. By also including a per-fragment rotation vector, we can significantly reduce the number of samples we'll need.

![[42-1-ssao-flow.png]]

As SSAO is a screen-space technique, we calculate its effect on each fragment on a screen-filled 2D quad. We can use G-buffers to render geometrical data, making SSAO perfectly suited in combination with deferred rendering.

## Geometry Pass
As we should have per-fragment position and normal data available from the scene objects, the fragment shader of the geometry stage is fairly simple.

```
#version 330 core

layout (location = 0) out vec4 gPosition;
layout (location = 1) out vec3 gNormal;
layout (location = 2) out vec4 gAlbedoSpec;

in V_OUT {
  vec3 position;
  vec3 normal;
  vec2 texCoords;
} f_in;

void main() {
  gPosition = vec4(f_in.position, 1.0);
  gNormal = f_in.normal;
  gAlbedoSpec.rgb = vec3(0.95);
}
```

Since SSAO is a screen-space technique where occlusion is calculated from the visible view, it makes sense to implement the algorithm in view-space. Therefore, the position and normal as supplied by the geometry stage's vertex shader are transformed to view space (multiplied by the view matrix as well.)

```
void main()
{
  vec4 viewPos = view * model * vec4(aPos, 1.0);
  gl_Position = projection * viewPos;
  v_out.texCoords = aTexCoords;
  v_out.position = viewPos.xyz;
  mat3 normalMatrix = mat3(transpose(inverse(view * model)));
  v_out.normal = normalize(normalMatrix * (invertedNormals ? -aNormal : aNormal));
}
```

The `gPosition` colour buffer is then configured as follows:

```c++
glGenTextures(1, &gPosition);
glBindTexture(GL_TEXTURE_2D, gPosition);
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGBA16F, windowWidth, windowHeight, 0, GL_RGBA, GL_FLOAT, NULL);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);
```

- This gives a position texture that we can use to obtain depth values for each of the kernel samples
- We store the positions in a floating point data format; this way position values aren't clamped to [0.0, 1.0] as we need the higher precision.
- The texture wrapping method of GL_CLAMP_TO_EDGE ensures we don't accidentally oversample position/depth values in screen-space outside the texture's default coordinate region.
