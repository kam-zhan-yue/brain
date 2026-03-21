## Summary
- Godot uses a reverse-z buffer, meaning 0.0 is far
- The depth value needs to be linearised to be used

The values returned from the depth texture are between 1.0 and 0.0, corresponding between near and far and are non-linear. When displaying depth directly from the depth texture, everything will look almost black unless it is very close due to that nonlinearity. In order to make the depth value align with world or model coordinates, we need to linearise the value.

When we apply the projection matrix to the vertex position, the z value is made nonlinear, so to linearise it, we multiply by the inverse of the projection matrix.

Firstly, we take the screen space coordinates and transform them into normalised device coordinates (NDC). NDC run -1.0 to 1.0 in x and y directions and from 0.0 to 1.0 in the z direction when using the Vulkan backend. Reconstruct the NDC using SCREEN_UV for the x and y axis, and the depth value for z.

Convert NDC to view space by multiplying the NDC by the inverse projection matrix. Recall that the view space gives positions relative to the camera, so the z value will give us the distance to the point.

```c++
float linearise_depth(vec2 uv) {
  float depth = texture(depth_buffer, uv).r;
  vec3 ndc = vec3(uv * 2.0 - 1.0, depth);
  vec4 view = params.inv_proj_mat * vec4(ndc, 1.0);
  view.xyz /= view.w;
  float linear_depth = -view.z;
  return linear_depth;
}
```

Because the camera is facing the negative z direction, the position will have a negative z value. In order to get a usable depth value, we have to negate view.z.

The world position can be constructed from the depth buffer using the `inv_view_mat` to transform the position from view space to world space.

```c++
vec4 world = inv_view_mat * inv_proj_mat * vec4(ndc, 1.0);
vec3 world_pos = world.xyz / world.w;
```