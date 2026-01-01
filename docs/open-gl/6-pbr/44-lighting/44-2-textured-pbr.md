Extending the system to now accept its surface parameters as textures instead of uniform values gives us per-fragment control over the surface material's properties.

```glsl
uniform sampler2D albedoMap;
uniform sampler2D normalMap;
uniform sampler2D metallicMap;
uniform sampler2D roughnessMap;
uniform sampler2D occlusionMap;

[...]

vec3 albedo = pow(texture(albedoMap, f_in.texCoords).rgb, vec3(2.2));
float metallic = texture(metallicMap, f_in.texCoords).r;
float roughness = texture(roughnessMap, f_in.texCoords).r;
float occlusion = texture(occlusionMap, f_in.texCoords).r;
vec3 N = getNormal();
```

The normal needs to be transformed from tangent to view space. This is a trick to do that:

```glsl
// trick to get tangent-normals to world-space
vec3 getNormal() {
  vec3 tangentNormal = texture(normalMap, f_in.texCoords).rgb * 2.0 - 1.0;
  vec3 Q1 = dFdx(f_in.position);
  vec3 Q2 = dFdy(f_in.position);
  vec2 st1 = dFdx(f_in.texCoords);
  vec2 st2 = dFdy(f_in.texCoords);

  vec3 N = normalize(f_in.normal);
  vec3 T = normalize(Q1 * st2.t - Q2 * st1.t);
  vec3 B = -normalize(cross(N, T));
  mat3 TBN = mat3(T, B, N);
  return normalize(TBN * tangentNormal);
}
```