GLSL has data types for specifying what kind of variable we want to work with. It has basic types like `int`, `float`, `double`. `bool`. `unit`. but it also has two container types that are heavily used:
- `vectors`
- `matrices`

## Vectors
A vector in GLSL is a 1, 2, 3 or 4 component container for any of the basic types mentioned. The vector database allows for "swizzling":
```c++
vec2 someVec;
vec4 differentVec = someVec.xyxx;
vec3 anotherVec = differentVec.xyz;
vec4 otherVec = someVec.xxxx + anotherVec.yxzy;
```

You can also pass vectors as arguments in different vector constructor calls to reduce the amount of arguments required.

```c++
vec2 vect = vec2(0.5, 0.7);
vec4 result = vec4(vect, 0.0, 0.0);
vec4 otherResult = vec4(result.xyz, 1.0);
```