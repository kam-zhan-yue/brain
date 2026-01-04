The main goal is to solve the integral for all the diffuse indirect lighting given the scene's irradiance in the form of a cubemap environment map. We know that we can get the radiance of the scene `L(p, wi)` in a particular direction by sampling a HDR environment map with direction `wi`. To solve the integral, we have to sample the scene's radiance from all possible directions within the hemisphere for each fragment.

However, it is computationally impossible to sample the environment's lighting from every possible direction as it is theoretically infinite. We can approximate the number of directions by taking a finite number of directions or samples, spaced uniformly or randomly in the hemisphere to get a fairly accurate approximation of the irradiance.

It is still too expensive to do this for every fragment in real-time, as the number of samples needs to be significantly large for decent results, so we pre-compute this. Since the orientation of the hemiphere decides where we capture the irradiance, we can pre-calculate the irradiance for every possible hemisphere orientation oriented around all outgoing direction `wo`.

![[45-0-split-2.png]]

Given any direction vector `wi` in the lighting pass, we can then sample the pre-computed irradiance map to retrieve the total diffuse irradiance from direction `wi`. To determine the amount of indirect diffuse (irradiant) light at a fragment surface, we retrieve the total irradiance form the hemisphere oriented around its surface normal. Obtaining the scene's irradiance is them as simple as:

```c++
vec3 irradiance = texture(irradianceMap, N).rgb;
```

We need to convolute the environment's lighting as converted to a cubemap. Given that for each fragment the surface's hemisphere is oriented along the normal vector N, convoluting a cubemap equals calculating the total averaged radiance of each direction along N.

![[45-2-hemisphere.png]]

We can directly take the converted cubemap, convolute it to a fragment shader, and capture its result as a new cubemap using a framebuffer that renders to all 6 face directions.

## Convolution

There are many ways to convolute the environment map. We will generate a fixed amount of sample vectors for each cubemap texel along a hemisphere oriented around the sample direction and average the results. the fixed amount of sample vectors will be uniformly spread inside the hemisphere.

The integral of the reflectance equation revolves around the solid angle `dw`, which is difficult to work with. Instead of that, we will integrate over its equivalent spherical coordinates.

![[45-2-spherical-coordinates.png]]

We will use the polar azimuth ø to sample around the ring of the hemisphere between 0 and 2PI and use the inclination angle (theta) between 0 and 0.5 PI to sample the increasing rings of the hemisphere. This updates the integral to:

![[45-2-integral-1.png]]

Solving the integral requires us to take a fixed number of discrete samples, so it translates to the following discrete version as based on the Riemann sum given n1 and n2 samples.

![[45-2-integral-2.png]]

As we sample both spherical values discretely, each sample will approximate or average an area on the hemisphere as the image before showed. Due to the properties of a spherical shape, the hemisphere's sample area gets smaller the higher the zenith angle Ø as the sample regions converge towards the centre top. To compensate for the smaller areas, we scale the area by sin theta.

Discretely sampling the hemisphere given the integral's spherical coordinates translates to the following fragment code.

```glsl
#version 330 core

in vec3 localPos;
out vec4 FragColor;

uniform samplerCube environmentCubemap;

const float PI = 3.14159265359;

void main() {
  // the sample direction equals the hemisphere's orientation
  vec3 normal = normalize(localPos);
  vec3 irradiance = vec3(0.0);

  vec3 up = vec3(0.0, 1.0, 0.0);
  vec3 right = cross(up, normal);
  up = cross(normal, right);

  float sampleDelta = 0.025;
  float nrSamples = 0.0;
  // increment the polar azimuth
  for (float phi = 0.0; phi < 2.0 * PI; phi += sampleDelta) {
    // increment the inclination angle
    for (float theta = 0.0; theta < 0.5; theta += sampleDelta) {
      // spherical to cartesian (in tangent space)
      vec3 tangentSample = vec3(sin(theta) * cos(phi), sin(theta) * sin(phi), cos(theta));
      // tangent space to world
      vec3 sampleVec = tangentSample.x * right + tangentSample.y * up + tangentSample.z * N;
      irradiance += texture(environmentMap, sampleVec).rgb * cos(theta) * sin(theta);
      nrSamples++;
    }
  }
  irradiance = PI * irradiance * (1.0 / nrSamples);

  FragColor = vec4(irradiance, 1.0);
}
```

- We specify a fixed `sampleDelta` to traverse the hemisphere, decreasing or increasing the sample delta will increase or decrease the accuracy respectively.
- From within both loops, we take both spherical coordinates to convert them into a 3D Cartesian sample vector, convert the sample from tangent to world space oriented around the normal, and use this sample vector to directly sample the HDR environment map.
- We add each sample result to `irradiance` which at the end we divide by the total number of samples taken, giving us the average sampled irradiance. We scale the sampled colour value by cos(theta) due to the light being weaker at larger angles and by sin(theta) to account for the smaller areas in higher hemisphere areas.

Now, we setup the OpenGL rendering code such that we can convolute the earlier captured `envCubemap`.

```c++
  // Pre-compute the irradiance diffuse map
  // --------------------------------------
  unsigned int irradianceMap;
  glGenTextures(1, &irradianceMap);
  glBindTexture(GL_TEXTURE_CUBE_MAP, irradianceMap);
  for (unsigned int i = 0; i < 6; ++i) {
    glTexImage2D(GL_TEXTURE_CUBE_MAP_POSITIVE_X + i, 0, GL_RGBA16F, 32, 32, 0, GL_RGB, GL_FLOAT, nullptr);
  }
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE);
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_R, GL_CLAMP_TO_EDGE);
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
  glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_MAG_FILTER, GL_LINEAR);

  irradianceShader.use();
  irradianceShader.setInt("environmentMap", 0);
  irradianceShader.setMat4("projection", captureProjection);
  glActiveTexture(GL_TEXTURE0);
  glBindTexture(GL_TEXTURE_CUBE_MAP, environmentCubemap);
  glViewport(0, 0, 32, 32);
  glBindFramebuffer(GL_FRAMEBUFFER, captureFBO);
  glBindRenderbuffer(GL_RENDERBUFFER, captureRBO);
  glRenderbufferStorage(GL_RENDERBUFFER, GL_DEPTH_COMPONENT24, 32, 32);
  for (unsigned int i = 0; i < 6; ++i) {
    irradianceShader.setMat4("view", captureViews[i]);
    glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_CUBE_MAP_POSITIVE_X + i, irradianceMap, 0);
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
    cube.draw();
  }
```

As the irradiance map averages all surrounding radiance uniformly, it doesn't have a lot of high frequency details, so we can store the map at a low resolution of around 32x32 and let OpenGL's linear filtering do most of the work.

We also needed to rescale the capture framebuffer to the new resolution.

After everything, the pre-computed irradiance map can be used for diffuse image based lighting.