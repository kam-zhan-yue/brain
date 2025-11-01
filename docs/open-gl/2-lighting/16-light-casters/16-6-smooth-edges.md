To create the effect of a smoothly-edged spotlight, we want to simulate a spotlight by having an inner and outer cone. We can set the inner cone as previous and have an outer cone that gradually dims the light from the inner to the edges of the outer cone.

To create an outer cone, we simply define another cosine value that represents the angle between the spotlight's direction vector and the outer cone's vector. Then if a fragment is between the inner and outer cone, it should calculate an intensity value between 0.0 and 1.0. If the fragment is inside the inner cone, the intensity is 1.0, and 0.0 if the fragment is outside the outer cone.

We calculate this with the following equation:
$$
I = \frac{\theta - \gamma}{\epsilon}
$$
- Gamma is the cosine of the outer cone
- Theta is the dot product of the fragment and the light
- Epsilon is the cosine difference between the inner and outer cone

We can clamp the property to clamp it between 0.0 and 1.0 to ensure the intensity won't end up outside the `[0, 1]` range.

```glsl
float intensity
```