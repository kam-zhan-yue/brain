Tone mapping is the process of transforming floating point colour values to LDR without losing too much detail, often accompanied with a specific stylistic colour balance.

## Reinhard Tone Mapping
One of the simpler tone mapping algorithms is to divide the entire HDR colour values to LDR colour values. This evenly balances out all brightness values onto LDR.

```
void main() {
  const float gamma = 2.2f;
  vec3 hdr = texture(colorBuffer, texCoords).rgb;

  // reinhard tone mapping
  vec3 mapped = hdr / (hdr + vec3(1.0));
  mapped = pow(mapped, vec3(1.0 / gamma));
  FragColor = vec4(mapped, 1.0);
}
```

With this applied, we don't lose any detail at the bright areas of the scene, but it tends to slightly favour brighter areas, making darker regions seem less detailed and distinct.

## Exposure Parameter
HDR images contain a lot of details visible at different exposure levels, If we have a scene that features a day and night cycle, it makes sense to use a lower exposure at daylight and a higher exposure at night time, similar to how the human eye adapts.

```
void main() {
  const float gamma = 2.2f;
  vec3 hdr = texture(colorBuffer, texCoords).rgb;

  // reinhard tone mapping
  vec3 mapped = vec3(1.0) - exp(-hdr * exposure);
  mapped = pow(mapped, vec3(1.0 / gamma));
  FragColor = vec4(mapped, 1.0);
}
```

With high exposure, the darker areas of the tunnel show significantly more detail. In contract, a low exposure removes the dark region details but allows us to see more detail in the bright areas of a scene.

![[39-2-exposure.png]]

