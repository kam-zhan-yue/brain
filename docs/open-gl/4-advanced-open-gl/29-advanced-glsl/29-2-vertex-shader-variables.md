---
id: 29-2-vertex-shader-variables
aliases: []
tags: []
---

## `gl_PointSize`
One of the render primitives we can choose is `GL_POINTS` in which case each single vertex is a primitive and rendered as a point. 

One output variable defined by GLSL is `gl_PointSize` that is a floatr variable where we can set the point's width and height in pixels. By setting the point's size in the vertex shader, we get per-vertex control over this point's dimensions.

Influencing the point sizes in the vertex shader is enabled with:

```c++
glEnable(GL_PROGRAM_POINT_POINT_SIZE);
```

We can set the point size equal to the clip-space position's z-value which is equal to the vertex's distance to the user. The point size should then increase the further we are from the vertices as the viewer.

```c++
void main() {
    gl_Position = projection * view * model * vec4(aPos, 1.0);
    gl_PointSize = gl_Position.z;
}
```

The result is that the points we've drawn are rendered larger the more we move away from them.

## `gl_VertexID`
The `gl_Poition` and `gl_PointSize` are output variables since their value is read as output from the vertex shader. `gl_VertexID` is an input variable that holds the current ID of the vertex we are drawing. When doing _indexed_ rendering, this variable holds the current index of the vertex we're drawing. When using non-indexed rendering (such a `glDrawArrays`), this variable hold the number of the currently processed vertex since the start of the render call.
