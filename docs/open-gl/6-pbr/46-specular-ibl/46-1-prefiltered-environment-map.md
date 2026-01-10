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

### Generating N Random Samples
There are multiple ways of generating the random samples. By default, each sample is completely random, but by utilising certain properties of semi-random sequences, we can generate sample vectors that are still random, but have interesting properties. For instance, we can do Monte Carlo integration on **low-discrepancy sequences**, which still generate random samples, but each sample is more evenly distributed.

![[46-1-low-discrepancy-sequences.png]]

When using a low-discrepancy sequence for generating the Monte Carlo sample vectors, the process is known as Quasi-Monte Carlo integration. These have a faster rate of convergence, which makes them interesting for performance heavy applications.
### Importance Sampling
We can use a technique known as importance sampling to achieve an even faster rate of convergence. When it comes to specular reflections, the reflected light vectors are constrained in a specular lobe with its size determined by the roughness of the surface. Seeing as any (quasi-)randomly generated sample outside the specular lobe isn't relevant to the specular integral, we can discard them.

> TLDR: Only sample vectors that would be relevant to the specular lobe. We generate sample vectors in some region constrained by the roughness oriented around the microfacet's halfway vector.

Combining Quasi-Monte Carlo sampling with a low-discrepancy sequence and biasing the sample vectors using importance sampling, we get a high rate of convergence. Because we reach the solution at a faster rate, we need significantly fewer samples to reach an approximation that is sufficient enough.

## A Low Discrepancy Sequence

We can compute a random-low discrepancy sequence based on the Quasi-Monte Carlo method known as the Hammersley Sequence as described by Holger Dammertz. This is based on the Van Der Corpus sequence which mirrors a decimal binary representation around its decimal point. 

Given some neat tricks, we can quite efficiently generate the Van Der Corpus sequence in a shader program which will be used to get a Hammersley sequence sample *i* over *N* total samples.

```glsl
// ----------------------------------------------------------------------------
// http://holger.dammertz.org/stuff/notes_HammersleyOnHemisphere.html
// efficient VanDerCorpus calculation.
float RadicalInverse_VdC(uint bits) {
     bits = (bits << 16u) | (bits >> 16u);
     bits = ((bits & 0x55555555u) << 1u) | ((bits & 0xAAAAAAAAu) >> 1u);
     bits = ((bits & 0x33333333u) << 2u) | ((bits & 0xCCCCCCCCu) >> 2u);
     bits = ((bits & 0x0F0F0F0Fu) << 4u) | ((bits & 0xF0F0F0F0u) >> 4u);
     bits = ((bits & 0x00FF00FFu) << 8u) | ((bits & 0xFF00FF00u) >> 8u);
     return float(bits) * 2.3283064365386963e-10; // / 0x100000000
}

vec2 Hammersley(uint i, uint N) {
  return vec2(float(i) / float(N), RadicalInverse_VdC(i));
}
```

##  GGX Importance Sampling

Instead of uniformly or randomly generating sample vectors over the integral's hemisphere, we'll generate sample vectors biased towards the general reflection orientation of the microsurface halfway vector based on the surface's roughness.

The sampling process will be similar to what we've seen before: begin a large loop, generate a random (low-discrepancy) sequence value, take the sequence value to generate a sample vector in tangent space, transform to world space, and sample the scene's radiance. What's different is that we now use a low-discrepancy sequence value as input to generate a sample vector.

```glsl
const uint SAMPLE_COUNT = 4096u;
for (uint i = 0u; i < SAMPLE_COUNT; ++i) {
	vec2 Xi = Hammersley(i, SAMPLE_COUNT);
}
```

Additionally, to build a sample vector, we need some way of orienting and biasing the sample vector towards the specular lobe of some surface roughness. We can take the NDF as described in the *Theory* chapter and combine the GGX NDF in the spheric sample vector process.

```glsl
vec3 ImportanceSampleGGX(vec2 Xi, vec3 N, float roughness) {
  float a = roughness;

  float phi = 2.0 * PI * Xi.x;
  float cosTheta = sqrt((1.0 = Xi.y) / (1.0 + (a*a - 1.0) * Xi.y));
  float sinTheta = sqrt(1.0 = cosTheta * cosTheta);

  // from spherical coordinates to cartesian coordinates
  vec3 H;
  H.x = cos(phi) * sinTheta;
  H.y = sin(phi) * sinTheta;
  H.z = cosTheta;

  // from tangent-space vector to world-space sample vector
  vec3 up = abs(N.z) < 0.999 ? vec3(0.0, 0.0, 1.0) : vec3(1.0, 0.0, 0.0);
  vec3 tangent = normalize(cross(up, N));
  vec3 bitangent = cross(N, tangent);
  
  // finally produce the sample vector
  vec3 sampleVec = tangent * H.x + bitangent * H.y + N * H.z;
  return normalize(sampleVec);
}
```

This gives us a sample vector somewhat oriented around the expected microsurface's halfway vector based on some input roughness and the low-discrepancy sequence value Xi. Epic Games uses the squared roughness for better visual results.

With the low-discrepancy Hammersley sequence and sample generation defined, we can finalise the pre-filter convolution shader.y

The final fragment shader looks like:

```glsl
#version 330 core

in vec2 texCoords;

out vec4 FragColor;

uniform samplerCube environmentMap;
uniform float roughness;

const float PI = 3.14159265359;

float RadicalInverse_VdC(uint bits);
vec2 Hammersley(uint i, uint N);
vec3 ImportanceSampleGGX(vec2 Xi, vec3 N, float roughness);

void main() {
  vec3 N = normalize(localPos);
  vec3 R = N;
  vec3 V = R;

  const uint SAMPLE_COUNT = 1024u;
  float totalWeight = 0.0;
  vec3 prefilteredColour = vec3(0.0);
  for (uint i = 0u; i < SAMPLE_COUNT; ++i) {
    // get a random vector from low-discrepancy sequence
    vec2 Xi = Hammersley(i, SAMPLE_COUNT);
    // get a random direction oriented in the importance sample
    vec3 H = ImportanceSampleGGX(Xi, N, roughness);
    vec3 L = normalize(2.0 * dot(V, H) * H - V);
    float NdotL = max(dot(N, L), 0.0);
    if (NdotL > 0.0) {
      prefilteredColour += texture(environmentMap, L).rgb * NdotL;
      totalWeight += NdotL;
    }
  }
  prefilteredColour = prefilteredColour / totalWeight;
  FragColor = vec4(prefilteredColour, 1.0);
}
```

We pre-filter the environment, based on some input roughness that varies over each mipmap level of the cubemap (from 0.0 to 1.0) and store the result in `prefilteredColour`. The resulting `prefilteredColour` is divided by the total sample weight, where samples with less influence on the final result contribute less to the final weight. (similar to the probability distribution function).

## Capturing Pre-Filter Mipmap Levels
What's left to do is to let OpenGL pre-filter the environment map with different roughness values over multiple mipmap levels. This is easy to do:

```c++
  Shader prefilterShader = Shader(
    (string(SHADER_DIR) + "/prefilter-vertex.glsl").c_str(),
    (string(SHADER_DIR) + "/prefilter-fragment.glsl").c_str()
  );
  prefilterShader.use();
  prefilterShader.setInt("environmentMap", 0);
  prefilterShader.setMat4("projection", captureProjection);
  glActiveTexture(GL_TEXTURE0);
  glBindTexture(GL_TEXTURE_CUBE_MAP, environmentCubemap);
  glBindFramebuffer(GL_FRAMEBUFFER, captureFBO);
  unsigned int maxMipLevels = 5;
  for (unsigned int mip = 0; mip < maxMipLevels; ++mip) {
    // resize the framebuffer according to the mip-level size
    unsigned int mipWidth = 128 * pow(0.5, mip);
    unsigned int mipHeight = 128 * pow(0.5, mip);
    glBindRenderbuffer(GL_RENDERBUFFER, captureRBO);
    glRenderbufferStorage(GL_RENDERBUFFER, GL_DEPTH_COMPONENT24, mipWidth, mipHeight);
    glViewport(0, 0, mipWidth, mipHeight);
    float roughness = (float)mip / (maxMipLevels - 1);
    prefilterShader.setFloat("roughness", roughness);
    for (unsigned int i = 0; i < 6; ++i) {
      prefilterShader.setMat4("view", captureViews[i]);
      glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_CUBE_MAP_POSITIVE_X + i, prefilterMap, mip);
      glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
      cube.draw();
    }
  }
```

- The process is similar to the irradiance map convolution, but this time we scale the framebuffer's dimensions to the appropriate mipmap scale. Each mipmap reduces the dimensions by a scale of 2.
- We also specify the mip level we're rendering to in `glFramebufferTexture2D`'s last parameter and pass the roughness we're pre-filtering for to the shader.

This should give us a properly pre-filtered environment map that returns blurrier reflections the higher mip level we access it from. If we use the pre-filtered environment map in the skybox shader and forcefully sample above its first mip level, we get something that looks like the blurrier version of the original environment. 

![[46-1-prefilter-map.png]]
