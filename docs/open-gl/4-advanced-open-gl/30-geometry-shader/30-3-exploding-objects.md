To explode an object, we can move each triangle along the direction of their normal vector over a small period of time.

To explode an object, we move each triangle along the direction of their normal vector over a small period of time. The normal vector is a vector that is perpendicular to the surface of the triangle. We can retrieve a vector perpendicular to two other vectors using the cross product.

We retrieve two vectors that are parallel to the surface of the triangle and retrieve the normal vector through cross product.

```glsl
vec3 getNormal() {
  vec3 a = vec3(gl_in[0].gl_Position) - vec3(gl_in[1].gl_Position);
  vec3 b = vec3(gl_in[1].gl_Position) - vec3(gl_in[2].gl_Position);
  return normalize(cross(a, b));
}
```

Since all three points lie on the triangle plane, subtracting any of its vectors will result in a vector parallel to the plane. However, if we switch a and b, we will get an orthogonal vector that goes in the opposite direction.

The explode function takes this normal vector along with a vertex position vector, translating it along the direction of the normal vector.

```glsl
vec3 explode(vec4 position, vec3 normal) {
  float magnitude = 2.0;
  vec3 direction = normal * ((sin(time) + 1.0) / 2.0) * magnitude;
  return position + vec4(direction, 0.0);
}
```



