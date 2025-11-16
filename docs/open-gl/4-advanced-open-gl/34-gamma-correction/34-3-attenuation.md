In a real physical world, lighting attenuates closely inversely proportional to the squared distance from a light source. This means light strength is reduced over distance to the light source squared.

```c++
float attenuation = 1.0 / (distance * distance);
```

However, this makes attenuation way too strong, giving lights a small radius that doesn't look physically right. Hence, other attenuation functions are used, or the linear equivalent is used.

```c++
float attenuation = 1.0 / distance;
```

The linear equivalent gives more plausible results when compared to its quadratic variant without gamma correction, but when we enable gamma correction, the linear attenuation looks too weak and the physically correct attenuation gives better results.

![[34-3-attenuation-grid.png]]

The cause is that light attenuation functions change brightness. We weren't visualising our scene in linear space, so we chose the attenuation functions that looked best on our monitor but were not physically correct.
- If we used the squared attenuation function without gamma correction, the attenuation function becomes (1.0/distance^2)^2.2 when displayed on a monitor, creating a much larger attenuation from what we originally anticipated.
- This explains why the linear attenuation makes more sense, as it is (1.0/distance)^2.2

Gamma correction allows us to do our shader/lighting calculations in linear space. Because linear space makes sense int he physical world, most physical equations now actually give good results. The more advanced lighting becomes, the easier it is to get good looking results with gamma correction.

> It is advised to only tweak lighting parameters when we have gamma correction in place.