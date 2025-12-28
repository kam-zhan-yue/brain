Parallax mapping is a technique based on normal mapping, but with different principles. It aims to significantly boost a textured surface's detail and give it a sense of depth.

Parallax mapping is related to the family of displacement mapping techniques that displace or offset vertices based on geometrical information stored inside a texture. One way to do this is to take a plane with 1000 vertices and displace each of these vertices based on a value in a texture that tells us the height of the plane at that specific area. Such a texture that contains height values per texel is called height map. We can derive a height map from the geometric properties of a simple brick surface.

When spanned over a plane, each vertex is displaced based on the sampled height value in the height map, transforming a flat plane to a rough bumpy surface based on a material's geometric properties. A flat plane displaced with a brick-likke heightmap can result in the following:

![[38-1-height-map.png]]

A problem with displacing vertices this way is that a plane needs to contain a huge amount of triangles to get a realistic displacement. Otherwise, the displacement looks too blocky. As each flat surface may then require over 10,000 vertices, this quickly becomes computationally infeasible.

We can achieve the same result with two triangles with parallax mapping, a displacement technique that doesn't require extra vertex data to convey depth, but uses a clever technique.

## Concept
The idea behind parallax mapping is to alter the texture coordinates in such a way that it looks like a fragment's surface is higher or lower than it actually is, based on the view direction and a heightmap.

![[38-1-parallax-diagram-1.png]]

The red line represents the values in the heightmap as the geometric surface representation of the brick surface and the vector V represents the surface to view direction (viewDir). If the plane would have actual displacement, the viewer would see the surface at point B. However, as our plane has no actual displacement, the view direction is calculated from point A, as we'd expect. Parallax mapping aims to offset the texture coordinates at fragment position A in such a way that we'd get texture coordinates at position B. We then use the texture coordinate at point B for all subsequent texture samples, making it look like the viewer is looking at point B.

The trick is to get the texture coordinates at point B from point A. Parallax mapping solves this by scaling the fragment-to-view direction vector V by the height at fragment A. We scale the length of V to be equal to a sampled value from the heightmap H(A) at fragment A. The scaled vector is P below.

![[38-1-parallax-diagram-2.png]]

> The length of P is equal to the distance H(A). The goal is to approximate B at H(P)

We take P and its vector coordinates that align with the plane as the texture coordinate offset. This works because P is calculated using a value from the heightmap. The higher a fragment's height, the more it gets displaced.

## Issues
This is a trick that gives good results most of the time, but it is a crude approximation to point B. When heights change rapidly over a surface, the results are more unrealistic as P will not end up close to B.

Another issue with parallax mapping is that it's difficult to figure out which coordinates to retrieve from P when the surface is arbitrarily rotated in some way. We can use the tangent space to figure out where the x and y components of P are aligned with the texture's surface.

By transforming the fragment-to-view direction to tangent space, the transformed P will have its x and y components aligned to the surface's tangent and bitangent vectors. As the tangent and bitangent vectors are pointing in the same direction as the surface's texture coordinates, we can take the x and y components of P as the texture coordinate offset, regardless of the surface's orientation.

## Implementation

We will use a 2D plane, calculate its tangent and bitangent vectors before sending it to the GPU. On the plane, we attach:
- A diffuse texture
- A normal map
- A displacement map
We use normal mapping with parallax mapping. Parallax mapping gives the illusion of displacing a surface, but the illusion breaks when the lighting doesn't match. As normal maps are often generated from heightmaps, using a normal map with the heightmap will make sure the lighting is in place with the displacement.

A displacement map is the inverse of the heightmap. With parallax mapping, it makes more sense to use the inverse of the heightmap as it is easier to fake depth than height on flat surfaces. This changes how we perceive parallax mapping as shown below:

![[38-1-parallax-diagram-3.png]]

We have A and B, but we obtain P by subtracting V from A. We obtain the depth values instead of height values by subtracting the sampled heightmap values from 1.0 in the shaders, or by simply inverting its texture values.

Parallax mapping is implemented in the fragment shader as the displacement effect is different all over a triangle's surface. In the fragment shader, we calculate the fragment-to-view direction vector V, so we need the view position and a fragment position in tangent space.

### Vertex Shader
We can use the same vertex shader we utilised previously that calculated everything in tangent space.

```
#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aNormal;
layout (location = 2) in vec2 aTexCoords;
layout (location = 3) in vec3 aTangent;
layout (location = 4) in vec3 aBitangent;

uniform mat4 model;
uniform mat4 view;
uniform mat4 projection;
uniform vec3 lightPos;
uniform vec3 viewPos;

out V_OUT {
  vec2 texCoords;
  vec3 tangentLightPos;
  vec3 tangentViewPos;
  vec3 tangentFragPos;
} v_out;

void main() {
  gl_Position = projection * view * model * vec4(aPos, 1.0);
  v_out.texCoords = aTexCoords;

  // Gram-Schmidt method
  mat3 normalMatrix = transpose(inverse(mat3(model)));
  vec3 T = normalize(normalMatrix * aTangent);
  vec3 N = normalize(normalMatrix * aNormal);
  // re-orthogonalise T with respect to N
  T = normalize(T - dot(T, N) * N);
  // retrieve B with the cross product of T and N
  vec3 B = cross(N, T);

  mat3 TBN = transpose(mat3(T, B, N));
  v_out.tangentLightPos = TBN * lightPos;
  v_out.tangentViewPos = TBN * viewPos;
  v_out.tangentFragPos = TBN * vec3(model * vec4(aPos, 1.0));
}
```

### Fragment Shader

The fragment shader then implements the parallax mapping logic.

```
vec2 parallaxMapping(vec2 texCoords, vec3 viewDir) {
  // Get the position of the depth map at the fragment position
  float depth = texture(depthMap, texCoords).r;
  vec2 p = viewDir.xy / viewDir.z * (height * heightScale);
  return texCoords - p;
}
```

- We take the original texture coordinates `texCoords` and use them to sample the depth as H(A).
- We calculate P as the x and y component of the tangent-space vector divided by its z component and scaled by `H(A)` with a `heightScale`
- We then subtract this vector P from the texture coordinates to get the final displaced texture coordinates

The division of `viewDir.xy` by `viewDir.z` is as follows:
- As the `viewDir` vector is normalised, `viewDir.z` will be somewhere in the range of 0.0 and 1.0. When the `viewDir` is parallel to the surfaced, its `z` component is close to 0.0 and the division returns a much larger P compared to if the `viewDir` is largely perpendicular to the surface
- We adjust the size of P in such a way that it offsets the texture coordinates at a larger scale when looking at a surface from an angle compared to when looking at it from the top; this gives more realistic results at angles.
- Some prefer to leave the division out as default Parallax Mapping could produce undesirable results at angles; the technique is then called "Parallax Mapping with Offset Limiting".
- Choosing which technique is a matter of personal preference.
- The resultant texture coordinates are then used to sample other textures (diffuse and normal) and this gives a very neat displacement effect

## Fixing Artifacts

Because parallax mapping tries to simulate depth, it is actually possible to have bricks overlap other bricks based on the direction you view them.

There are still a few weird border artifacts at the edge of the parallax mapped plane. This happens because at the edges of the plane, the displaced texture coordinates can oversample outside the range [0, 1]. This gives unrealistic results based on the texture's wrapping mode. A trick to solve this issue is to discard the fragment whenever it samples outside the default texture coordinate range.

Although it looks great, it breaks down when looking at it from an angle and gives incorrect results with steep height changes.

![[38-1-limitations.png]]

> The reason that it doesn't work is because it is a crude approximation of displacement mapping. There are extra tricks that lets us get almost perfect results with steep height changes which involves taking multiple samples to find the closest point to P.
