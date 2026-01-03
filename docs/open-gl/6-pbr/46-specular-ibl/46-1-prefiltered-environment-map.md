Pre-filtering an environment map is quite similar to how we convoluted an irradiance map. the difference being that we now account for roughness and store sequentially rougher reflections in the pre-filtered map's mip levels.

First, we need to generate a new cubemap to hold the pre-filtered environment map data. to make sure we allocate enough memory for its mip levels, we call `glGenerateMipmap` as an easy way to allocate the required amount of memory

```c++
unsigned int generatePrefilter() {
  unsigned int prefilterMap;
  glGenTextures(1, &prefilterMap);
  glBindTexture(GL_TEXTURE_2D, prefilterMap);
  for (unsigned int i = 0; i < 6; ++i) {
    glTexImage2D(GL_TEXTURE_CUBE_MAP_POSITIVE_X + i, 0, GL_RGBA16F, 128, 128, 0, GL_RGB, GL_FLOAT, nullptr);
  }

  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE);
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_R, GL_CLAMP_TO_EDGE);
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_MAG_FILTER, GL_LINEAR);

  return prefilterMap;
}
```

- Because we plan to sample `prefilterMap`'s mipmaps, we set the minification filter to `GL_LINEAR_MIPMAP_LINEAR` to enable trilinear filtering. 
- We store the pre-filtered specular reflections in a per-face resolution of 128 by 128 at its base mip level. This is likely to be enough for most reflections, but if you have a large number of smooth materials, you may want to increase the resolution

Previously, we generated sample vectors uniformly spread over the hemisphere using spherical coordinates. This is less efficient for specular reflections. When it comes to specular reflections, based on the roughness of a surface, the light reflects closely or roughly around a reflection vector *r* over a normal *n*, but around the reflection vector nonetheless:

![[46-1-specular-reflection.png]]

The general shape of outgoing light reflections is known as the specular lobe. As roughness increases, the specular lobe's size increases; and the shape of the specular lobe changes on varying incoming light directions. The shape of the specular lobe is thus highly dependent on the material.

When it comes to the microfacet model, we can imagine the specular lobe as the reflection orientation about the microfacet halfway vectors given some incoming light direction. Seeing as most light rays end up in a specular lobe reflected around the microfacet halfway vectors, it makes sense to generate the sample vectors in a similar fashion. This is known as importance sampling

## Monte Carlo Integration and Importance Sampling

To fully understand importance sampling, we must first understand Monte Carlo integration. Monte Carlo integration revolves mostly around a combination of statistics and probability theory. It helps us in discretely solving the problem of figuring out some statistic or value of a population without having to take all of the population into consideration.

For instance, let's say you want to count the average height of all citizens of a country. To get the result, you could measure every citizen and average their height which will give you the exact answer. However, since most countries have a considerable population, this is not realistic.

A different approach would be to pick a much smaller completely random subset of this population. You will get an answer that is relatively close to the ground truth. This is known as the **law of large numbers**.

Monte Carlo integration builds on this law of large numbers and takes the same approach in solving an integral. Rather than solving the integral for all possible sample values, we generate *N* sample values randomly picked from the total population and average. As N increases, we are guaranteed to get a result closer to the exact answer of the integral.

![[46-1-monte-carlo-integral.png]]

To solve the integral, we take N random samples over the population (a to b), add them together, and divide by the total number of samples to average them. `pdf` stands for the probability density function that tells us the probability of a specific sample occurring over the total sample set. For instance, the probability density function of a population may look like:

![[46-1-probability-density-function.png]]

From this graph, we can see that if we take any random sample of the population, there is a higher chance of picking someone who is 170cm tall than someone who is 150cm tall.

When it comes to the Monte Carlo integration, some samples may have a higher probability of being generated than others. This is why for any general Monte Carlo estimation, we divide or multiply the sampled value by the probability density function.

So far, our integrals have been unbiased, meaning that we will eventually converge to the exact solution of the integral. However, some Monte Carlo integrals are biased, meaning that the generated samples aren't completely random and are focused to a specific value or direction. Biased Monte Carlo estimators have a faster rate of convergence, meaning that they can converge to the exact solution at a much faster range, but due to their biased nature, it is likely they won't ever converge to the exact solution.

This is an acceptable tradeoff in computer graphics. Monte Carlo integration is used to approximate continuous integrals in a discrete and efficient fashion:
- Take any area / volume to sample over
- Generate N amount of random samples
- Sum and weigh every sample contribution to the final result

### Generating N  Random Samples
There are multiple ways of generating the random samples. By default, each sample is completely random, but by utilising certain properties of semi-random sequences, we can generate sample vectors that are still random, but have interesting properties. For instance, we can do Monte Carlo integration on **low-discrepancy sequences**, which still generate random samples, but each sample is more evenly distributed.

![[46-1-low-discrepancy-sequences.png]]

When using a low-discrepancy sequence for generating the Monte Carlo sample vectors, the process is known as Quasi-Monte Carlo integration. These have a faster rate of convergence, which makes them interesting for performance heavy applications.

### Importance Sampling
We can use a technique known as importance sampling to achieve an even faster rate of convergence. When it comes to specular reflections, the reflected light vectors are constrained in a specular lobe with its size determined by the roughness of the surface. Seeing as any (quasi-)randomly generated sample outside the specular lobe isn't relevant to the specular integral, we can discard them.

> TLDR: Only sample vectors that would be relevant to the specular lobe. We generate sample vectors in some region constrained by the roughness oriented around the microfacet's halfway vector.

Combining Quasi-Monte Carlo sampling with a low-discrepancy sequence and biasing the sample vectors using importance sampling, we get a high rate of convergence. Because we reach the solution at a faster rate, we need significantly fewer samples to reach an approximation that is sufficient enough.

