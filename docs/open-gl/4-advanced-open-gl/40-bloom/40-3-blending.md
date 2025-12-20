With the scene's HDR texture and a blurred brightness texture, we just need to combine the two to achieve bloom. In the final fragment shader, we additively blend both textures.

```
#version 330 core

in vec2 texCoords;

uniform sampler2D colorBuffer;
uniform sampler2D blurBuffer;
uniform float exposure;

out vec4 FragColor;

void main() {
  const float gamma = 2.2f;
  vec3 hdr = texture(colorBuffer, texCoords).rgb;
  vec3 blur = texture(blurBuffer, texCoords).rgb;
  hdr += blur; // additive blending

  // reinhard tone mapping
  vec3 mapped = vec3(1.0) - exp(-hdr * exposure);
  mapped = pow(mapped, vec3(1.0 / gamma));
  FragColor = vec4(mapped, 1.0);
}
```

One thing to note is that we add the bloom effect before we apply tone mapping. This way, the added brightness of the bloom is softly transformed to LDR with better relative lighting as a result.

![[40-3-bloom.png]]

The cubes now appear brighter and give a better illusion as light emitting objects.

By taking more samples along a larger radius, or repeating the blur filter an extra number of times, we can improve the blur effect. As the quality of the blur directly correlates to the quality of the bloom effect, improving the blur can make a significant improvement. Some of these improvements combine blur filters with varying size blur kernels or use multiple Gaussian curves to selectively combine weights. 