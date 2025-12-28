As it is difficult nor plausible to generate a sample kernel for each surface normal direction, we will generate a sample kernel in tangent space, with the normal vector pointing in the positive z direction.

```c++
vector<glm::vec3> generateSampleKernel() {
  uniform_real_distribution<float> randomFloats(0.0, 1.0);
  default_random_engine generator;
  vector<glm::vec3> sampleKernel;
  for (unsigned int i=0; i<64; ++i) {
    glm::vec3 sample(
      randomFloats(generator) * 2.0 - 1.0,
      randomFloats(generator) * 2.0 - 1.0,
      randomFloats(generator),
    );
    sample = glm::normalize(sample);
    sample += randomFloats(generator);
    sampleKernel.push_back(sample);
  }
  return sampleKernel;
}
```

- We vary the x and y direction in tangent space between -1.0 and 1.0 and vary the z direction of the samples between 0.0 and 1.0. If we varied the z direction between -1.0 and 1.0, we would have a sphere sample kernel.
- As the sample kernel will be oriented along the surface normal, the resulting sample vectors will all end up in the hemisphere.
- Currently, all samples are randomly distributed in the sample kernel, but we'd rather place a larger weight on occlusions close to the actual fragment. We want to distribute more kernel samples closer to the origin

This can be done with an accelerating interpolation function, giving us a kernel distribution that places most samples closer to its origin.

```c++
  for (unsigned int i=0; i<64; ++i) {
    glm::vec3 sample(
      randomFloats(generator) * 2.0 - 1.0,
      randomFloats(generator) * 2.0 - 1.0,
      randomFloats(generator),
    );
    sample = glm::normalize(sample);
    sample *= randomFloats(generator);
    float scale = (float)i / 64.0;
    scale = lerp_float(0.1f, 1.0f, scale * scale);
    sample *= scale;
    sampleKernel.push_back(sample);
  }
```

Each of the kernel samples will be used to offset the view-space fragment position to sample surrounding geometry. We need a lot of samples in view-space in order to get realistic results, which may be heavy on performance. However, if we can introduce semi-random rotation / noise on a per-fragment basis, we can significantly reduce the number of samples required.