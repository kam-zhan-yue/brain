The pre-filtered map is one part of the split-sum approximation. This covers the left part of the equation. The other part involves pre-computing the BRDF.

![[46-0-split-sum-approximation.png]]
 The right side requires us to convolute the BRDF equation over the angle `n * w_o`, the surface roughness, and Fresnel's F0. This is similar to integrating the specular BRDF with a solid-white environment or a constant radiance Li of 1.0. Convoluting the BRDF over 3 variables is a bit much, but we can move F0 out of the specular BRDF equation. 

![[46-3-brdf-1.png]]

![[46-3-brdf-2.png]]

The two resulting integrals represent a scale and a bias to F0 respectively. Note that as `f_r(p, wi, wo)` already contains a term for F, they both cancelled out, removing F from fr.

In a similar fashion to the earlier convoluted environment maps, we can convolute the BRDF equations on their inputs; the angle between n and wo and the roughness. We store the convoluted results in a 2D lookup texture (LUT) known as a BRDF integration map that we later use in our PBR lighting shader to get the final convoluted specular result.

The BRDF convolution shader operates on a 2D plane, using its 2D texture coordinates directly as inputs to the BRDF convolution: NdotV and roughness. The convolution code is largely similar to the prefilter convolution, except not that it processes the sample vector according to our BRDF's geometry function and Fresnel-Schlick's approximation.

```glsl
void main() {
  vec2 integrateBRDF = IntegrateBRDF(texCoords.x, texCoords.y);
  FragColor = integrateBRDF;
}

vec2 IntegrateBRDF(float NdotV, float roughness) {
  vec3 V;
  V.x = sqrt(1.0 - NdotV * NdotV);
  V.y = 0.0;
  V.z = NdotV;

  float A = 0.0;
  float B = 0.0;

  vec3 N = vec3(0.0, 0.0, 1.0);
  
  const uint SAMPLE_COUNT = 1024u;
  for (uint i = 0u; i < SAMPLE_COUNT; ++i) {
    vec2 Xi = Hammersley(i, SAMPLE_COUNT);
    vec3 H = ImportanceSampleGGX(Xi, N, roughness);
    vec3 L = normalize(2.0 * dot(V, H) * H - V);

    float NdotL = max(L.z, 0.0);
    float NdotH = max(H.z, 0.0);
    float VdotH = max(dot(V, H), 0.0);

    if (NdotL > 0.0) {
      float G = GeometrySmith(N, V, L, roughness);
      float G_Vis = (G * VdotH) / (NdotH * NdotV);
      float Fc = pow(1.0 - VdotH, 5.0);
      
      A += (1.0 - Fc) * G_Vis;
      B += Fc * G_Vis;
    }
  }

  A /= float(SAMPLE_COUNT);
  B /= float(SAMPLE_COUNT);
  return vec2(A, B);
}
```

The BRDF convolution is an accumulation of everything in PBR so far. We take both the angle and roughness as input, generate a sample vector with importance sampling, process it over the geometry and the derived Fresnel term of the BRDF, and output both a scale and a bias to F0 for each sample, averaging them in the end.

You may recall from the *Theory* chapter that the geometry term of the BRDF is slightly different when used alongside IBL as its *k* variable has a slightly difference interpretation:

$$ k_{direct} = \frac{(a+1)^2}{8} $$
$$ k_{IBL} = \frac{a^2}{2} $$
Since the BRDF convolution is part of the specular IBL integral, we will use k_IBL for the Schlick-GGX geometry function.
```glsl
float GeometrySchlickGGX(float NdotV, float roughness) {
  float a = roughness;
  float k = (a * a) / 2.0;
  float numerator = NdotV;
  float denominator = NdotV * (1.0 - k) + k;
  return numerator / denominator;
}

float GeometrySmith(vec3 N, vec3 V, vec3 L, float roughness) {
  float NdotV = max(dot(N, V), 0.0);
  float NdotL = max(dot(N, L), 0.0);
  float ggx1 = GeometrySchlickGGX(NdotV, roughness);
  float ggx2 = GeometrySchlickGGX(NdotL, roughness);
  return ggx1 * ggx2;
}
```

Note that while *k* takes *a* as its parameter, we didn't square `roughness` as `a` as we originall did for other interpretations; likely as `a` is squared here already. This could be an inconsistency on Epic Games' part or the original Disney paper, but directly translating `roughness` to `a` gives the BRDF integration map that is identical to the Epic Games' version.

Finally, to store the BRDF convolution result, we generate a 2D texture of a 512 by 512 resolution.

```c++
// Pre-compute the BRDF Map
// --------------------------------------
unsigned int brdfLUTTexture;
glGenTextures(1, &brdfLUTTexture);

// pre-allocate enough memory for the LUT texture
glBindTexture(GL_TEXTURE_2D, brdfLUTTexture);
glTexImage2D(GL_TEXTURE_2D, 0, GL_RG16F, 512, 512, 0, GL_RG, GL_FLOAT, 0);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);

// run the shader over an NDC screen-space quad
glBindFramebuffer(GL_FRAMEBUFFER, captureFBO);
glBindRenderbuffer(GL_RENDERBUFFER, captureRBO);
glRenderbufferStorage(GL_RENDERBUFFER, GL_DEPTH_COMPONENT24, 512, 512);
glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_2D, brdfLUTTexture, 0);
glViewport(0, 0, 512, 512);
brdfShader.use();
glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
Quad quad = Quad();
quad.draw();
```

- We use a 16-bit precision floating format as recommended by Epic Games.
- We set wrapping mode to `GL_CLAMP_TO_EDGE` to prevent edge sampling artifacts
- The convoluted BRDF part of the split sum integral should give the following result:

![[46-3-brdf-texture.png]]

With both the pre-filtered environment and the BRDF 2D LUT, we can re-construct the indirect specular integral according to split sum approximation. The combined result then acts as the indirect or ambient specular light.

> NOTE: For this to work, I had to change the texture to return a vec4 instead of a vec2. The adjusted code looks like

```glsl
void main() {
    vec2 integratedBRDF = IntegrateBRDF(TexCoords.x, TexCoords.y);
    FragColor = vec4(integratedBRDF, 0.0, 1.0);
}
```

```c++
// use GL_RGBA because old method was not fking working
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGBA16F, 512, 512, 0, GL_RGBA, GL_FLOAT, 0);
```