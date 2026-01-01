![[43-4-final-equation.png]]

## Fragment Shader

This fragment shader implements the previously described PBR models.

```glsl
#version 330 core

out vec4 FragColor;

struct Light {
  vec3 position;
  vec3 colour;
};

in V_OUT {
  vec3 position;
  vec3 normal;
  vec2 texCoords;
} f_in;

uniform vec3 camPos;
uniform vec3 albedo;
uniform float metallic;
uniform float roughness;
uniform float ambientOcclusion;

#define NUM_LIGHTS 4
uniform Light lights[NUM_LIGHTS];
```

## Direct Lighting

To satisfy the reflectance equation, we loop over each light source, calculate its individual radiance and sum its contributions scaled by the BRDF and the light's incident angle.

```glsl
  vec3 Lo = vec3(0.0);
  for (int i = 0; i < NUM_LIGHTS; ++i) {
    vec3 L = normalize(lights[i].position - f_in.position);
    vec3 H = normalize(V + L);

    float distance = length(lights[i].position - f_in.position);
    float attenuation = 1.0 / (distance * distance);
    vec3 radiance = lights[i].colour * attenuation;
  }
```

As we calculate lighting in linear space, we attenuate light sources by the more physically correct inverse-square law.

Then, for each light we want to calculate the full Cook-Torrence specular BRDF term.

The first thing we want to do is to calculate the ratio between specular and diffuse reflection, or how much the surface reflects light versus how much it refracts light. This is calculates by the Fresnel-Schlick equation:

```glsl
vec3 fresnelSchlick(float cosTheta, vec3 F0) {
  return F0 + (1.0 - F0) * pow(1.0 - cosTheta, 5.0);
}

[...]

vec3 F0 = vec3(0.04);
F0 = mix(F0, albedo, metallic);
vec3 F = fresnelSchlick(max(dot(H, V), 0.0), F0);
```

- The `F0` parameter is known as the surface reflection at zero incidence (base reflectivity)
- This varies per material and is tinted on metals
- In the PBR metallic workflow, we make the assumption that most dielectric surfaces look visually correct with a constant of `0.04`
- We specify `F0` for metallic surfaces by their albedo value

We can then calculate the normal distribution D and the geometry function G.

```
float distributionGGX(vec3 N, vec3 H, float roughness) {
  float a = roughness * roughness;
  float a2 = a * a;
  float NdotH = max(dot(N, H), 0.0);
  float NdotH2 = NdotH * NdotH;

  float numerator = a2;
  float denominator = NdotH * (a2 - 1.0) + 1.0;
  denominator = PI * denominator * denominator;
  return numerator / denominator;
}

float geometrySchlickGGX(float NdotV, float roughness) {
  float r = (roughness + 1.0);
  float k = (r * r) / 8.0;
  float numerator = NdotV;
  float denominator = NdotV * (1 - k) + k;
  return numerator / denominator;
}

float geometrySmith(vec3 N, vec3 V, vec3 L, float roughness) {
  float NdotV = max(dot(N, V), 0.0);
  float NdotL = max(dot(N, L), 0.0);
  float ggx1 = geometrySchlickGGX(NdotV, roughness);
  float ggx2 = geometrySchlickGGX(NdotL, roughness);
  return ggx1 * ggx2;
}

float D = distributionGGX(N, H, roughness);
vec3 F = fresnelSchlick(max(dot(H, V), 0.0), F0);
float G = geometrySmith(N, V, L, roughness);
```

Finally, we can calculate the Cook-Torrance BRDF:

```glsl
vec3 numerator = D * F * G;
float denominator = 4.0 * max(dot(N, V), 0.0) * max(dot(N, L), 0.0);
vec3 specular = numerator / max(denominator, 0.001);
```

> We constrain the denominator to 0.001 to prevent a division by zero error

Finally, we can calculate each light's contribution to the reflectance equation. As the Fresnel value directly corresponds to `ks`, we can use `F` to denote the specular contribution of any light that hits the surface. From `ks`, we can then calculate the ratio of reflection `kd`.

Because metallic surfaces don't refract light and thus have no diffuse reflections, we enforce this property by nullifying `kD` if the surface is metallic. This gives us the final data we need to calculate each light's outgoing reflectance value:

```glsl
	vec3 kS = F;
	vec3 kD = vec3(1.0) - kS;
	kD *= 1.0 - metallic;
	float NdotL = max(dot(N, L), 0.0);
	Lo += (kD * albedo / PI + specular) * radiance * NdotL;
```

The resulting `Lo` value (the outgoing radiance) is the result of the reflectance equation's integral over the hemisphere, We don't have to try and solve the integral for all possible incoming light directions as we know the incoming light directions that can influence the fragment. Because of this, we can loop over these incoming directions for each fragment.

Next, we add an (improvised) ambient term to the direct lighting result `Lo`.

```glsl
vec3 ambient = vec3(1.0) * albedo * ambientOcclusion;
vec3 colour = ambient + Lo;
FragColor = vec4(color, 1.0);
```

## Linear and HDR Rendering

We assumed our calculations to be in linear colour space and to account for this, we need to gamma correct at the end of the shader. Calculating lighting in linear space is important as PBR requires all inputs to be linear.

Additionally, we want light inputs to be close to their physical equivalents such that their radiance or colour values can vary wildly over a high spectrum of values. As a result, `Lo` can rapidly grow really high, which then gets clamped between `0.0` and `1.0` due to the default low dynamic range (LDR) output.

We fix this by taking `Lo` and tone mapping the high dynamic range correctly to LDR before gamma correction.

Taking both linear colour space and high dynamic range into account is incredibly important in a PBR pipeline. Without these, it is impossible to properly capture the high and low details of varying light intensities and calculations end up incorrect and visually unpleasing.

## Full Fragment Shader
```glsl
#version 330 core

out vec4 FragColor;

struct Light {
  vec3 position;
  vec3 colour;
};

in V_OUT {
  vec3 position;
  vec3 normal;
  vec2 texCoords;
} f_in;

uniform vec3 camPos;
uniform vec3 albedo;
uniform float metallic;
uniform float roughness;
uniform float ambientOcclusion;

#define NUM_LIGHTS 4
uniform Light lights[NUM_LIGHTS];

const float PI = 3.14159265359;

vec3 fresnelSchlick(float cosTheta, vec3 F0) {
  return F0 + (1.0 - F0) * pow(1.0 - cosTheta, 5.0);
}

float distributionGGX(vec3 N, vec3 H, float roughness) {
  float a = roughness * roughness;
  float a2 = a * a;
  float NdotH = max(dot(N, H), 0.0);
  float NdotH2 = NdotH * NdotH;

  float numerator = a2;
  float denominator = NdotH * (a2 - 1.0) + 1.0;
  denominator = PI * denominator * denominator;
  return numerator / denominator;
}

float geometrySchlickGGX(float NdotV, float roughness) {
  float r = (roughness + 1.0);
  float k = (r * r) / 8.0;
  float numerator = NdotV;
  float denominator = NdotV * (1 - k) + k;
  return numerator / denominator;
}

float geometrySmith(vec3 N, vec3 V, vec3 L, float roughness) {
  float NdotV = max(dot(N, V), 0.0);
  float NdotL = max(dot(N, L), 0.0);
  float ggx1 = geometrySchlickGGX(NdotV, roughness);
  float ggx2 = geometrySchlickGGX(NdotL, roughness);
  return ggx1 * ggx2;
}

void main() {
  vec3 N = normalize(f_in.normal);
  vec3 V = normalize(camPos - f_in.position);

  // base reflectivity (assume 0.04 for dielectrics)
  vec3 F0 = vec3(0.04);
  F0 = mix(F0, albedo, metallic);

  // reflectance equation
  vec3 Lo = vec3(0.0);
  for (int i = 0; i < NUM_LIGHTS; ++i) {
    // calculate per-light radiance
    vec3 L = normalize(lights[i].position - f_in.position);
    vec3 H = normalize(V + L);
    float distance = length(lights[i].position - f_in.position);
    float attenuation = 1.0 / (distance * distance);
    vec3 radiance = lights[i].colour * attenuation;

    // Cook-Torrance BRDF
    float D = distributionGGX(N, H, roughness);
    vec3 F = fresnelSchlick(clamp(dot(H, V), 0.0, 1.0), F0);
    float G = geometrySmith(N, V, L, roughness);

    vec3 numerator = D * F * G;
    float denominator = 4.0 * max(dot(N, V), 0.0) * max(dot(N, L), 0.0) + 0.0001;
    vec3 specular = numerator / denominator;

    // light reflected (kS) is equal to Fresnel
    vec3 kS = F;
    // light refracted follows law of energy conservation
    vec3 kD = vec3(1.0) - kS;
    // multiply by inverse metalness such that only non-metals have diffuse lighting
    // since pure metals have no diffuse light
    kD *= 1.0 - metallic;

    // scale light by NdotL
    float NdotL = max(dot(N, L), 0.0);

    // add to outgoing radiance Lo
    Lo += (kD * albedo / PI + specular) * radiance * NdotL;
  }

  // ambient lighting
  vec3 ambient = vec3(0.5) * albedo * ambientOcclusion;
  vec3 colour = ambient + Lo;

  // HDR tone mapping using Reinhard operator
  colour = colour / (colour + vec3(1.0));
  // gamma correction
  colour = pow(colour, vec3(1.0 / 2.2));

  FragColor = vec4(colour, 1.0);
}
```