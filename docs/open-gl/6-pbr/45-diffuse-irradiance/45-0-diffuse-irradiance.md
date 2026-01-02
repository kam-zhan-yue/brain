Image Based Lighting (IBL) is a collection of techniques to light objects, not by direct analytical lights, but by treating the surrounding environment as one big light source. This is generally accomplished by manipulating a cubemap environment map such that we can directly use it in our lighting equations: treating each cubemap texel as a light emitter. This way we can effectively capture an environment's global lighting and general feel, giving objects a better sense of belonging in their environment.

As image based lighting algorithms capture the lighting of some (global) environment, its input is considered a more precise form of ambient lighting, even a crude approximation of global illumination. This makes IBL interesting for PBR objects look significantly more physically accurate when we take the environment's lighting into account.

![[43-4-final-equation.png]]

Looking back at the equation, we want to solve the integral for all incoming light directions `w` over the hemisphere omega. This was easy as we knew the incoming light directions that contributed to the integral.

This time, every incoming light direction from the surrounding environment could potentially have some radiance, making it less trivial to solve the integral. This gives two main requirements for solving the integral:
- We need some way to retrieve the scene's radiance given any direction vector w
- Solving the integral needs to be fast and real-time

## Requirement 1 - Radiance
Given the environmental cubemap, we can visualise every texel of the cubemap as one single emitting light source. By sampling this cubemap with any direction vector, we retrieve the scene's radiance from that direction.

```c++
vec3 radiance = texture(environmentCubemap, w_i).rgb;
```

However, solving the integral requires us to sample the environment map from not just one direction, but all possible directions in the hemisphere, which is far too expensive for each fragment shader invocation. To solve the integral in a more efficient fashion, we can pre-compute most of the computations.

In the reflectance equation, the diffuse `kD` and the specular `kS` term of the BRDF are independent from each other, meaning that we can split the equation into two.

![[45-0-split-1.png]]

Taking an even closer look at the diffuse integral, the diffuse lambert term is a constant term and not dependent on any of the integral variables. Given this, we can move the constant term out of the diffuse integral.

![[45-0-split-2.png]]

This gives us an integral that only depends on `wi` (assuming p is at the centre of the environment map). With this knowledge, we can pre-compute a new cubemap that storews in each texel the diffuse integral's result by convolution.

> Convolution is applying some computation to each entry in a data set considering all other entries in the data set; the data set being the scene's radiance or environment map. Thus for every sample direction in the cubemap, we take all other sample directions in the hemisphere into account.

## Requirement 2: Optimisation
To convolute an environmental map, we solve the integral for each output `wo` sample direction by discretely sampling a large number of directions `wi` over the hemisphere and averaging their radiance. The hemisphere we build the sample directions `wi` from is oriented towards the output `wo` sample direction that we want to convolute.

This pre-computed cubemap that for each sample direction `wo` stores the integral result, can be thought of as the pre-computed sum of all indirect diffuse light of the scene hitting some surface aligned along direction `wo`. Such a cubemap is known as an irradiance map seeing as the convoluted cubemap effectively allows us to directly sample the scene's (pre-computed) irradiance from any direction.

> The radiance equation also depends on a position *p*, which we've assumed to be the centre of the irradiance map. This does mean all diffuse indirect light must come from a single environment map, which may break the illusion of reality (especially indoors). Render engines solve this by placing reflection probes all over the scene where each reflection probe calculates its own irradiance map of its surroundings. This way, the irradiance at position p is the interpolated irradiance between its closest reflection probes.

Below is an example of a cubemap environment map and its resulting irradiance map, averaging the scene's radiance for every direction `wo`.

![[45-0-irradiance-map.png]]

By storing the convoluted result in each cubemap, the irradiance map somewhat displays an average colour or lighting display of the environment. Sampling any direction from this environment map will give us the scene's irradiance in that particular direction.