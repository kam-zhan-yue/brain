---
id: 29-3-fragment-shader-variables
aliases: []
tags: []
---

## `gl_FragCoord`

The z component of the `gl_FragCoord` is equal to the depth value of that particular fragment. However, we can also use the x and y component of that vector.

The x and y component are the window or screen-space coordinates of the fragment, originating from the bottom-left of the window. We specified a render window of 800x600 with `glViewport` so the screen space coordinate of the fragment will have x values between 0 and 800, y will be between 0 and 600.

We can calculate a different colour value baed on the screen coordinate of the fragment. We can split the screen in two by rendering one output to the left and another to the right. An example fragment shader that outputs a different colour based on the screen coordinates is:

```glsl
void main() {
    if (gl_FragCoord.x < 400)
        FragColor = vec4(1.0, 0.0, 0.0, 1.0);
    else
        FragColor = vec4(0.0, 1.0, 0.0, 1.0);
}
```

## `gl_FrontFacing`
OpenGL is able to figure out if a face is a front or back face due to the winding order of the vertices. The `gl_FrontFacing` variable tells us if the current fragment is part of a front-facing or a back-facing face. We can decide to output different colour for all back faces.

`gl_FrontFacing` is a bool that is true if the fragment is part of a front face and false otherwise.

Note that this is not compatible with face culling.

```glsl
void main() {
    if (gl_FrontFacing)
        FragColor = vec4(1.0, 0.0, 0.0, 1.0);
    else
        FragColor = vec4(0.0, 1.0, 0.0, 1.0);
}
```

## `gl_FragDepth`
While `gl_FragCoord` is a read-only variable, `gl_FragDepth` is an output variable that we can use to manually set the depth value of the fragment within the shader.

To set the depth value we write any value between 0.0 and 1.0.

```glsl
gl_FragDepth = 0.0;
```

If the shader does not write anything to `gl_FragDepth`, the variable will automatically take `gl_FragCoord.z`

However, OpenGL disables early depth testing as soon as we write to `gl_FragDepth` because OpenGL cannot know what depth value the fragment will have before we run the fragment shader, since the fragment shader may actually change this value.

By writing to `gl_FragDepth`, you need to take this performance penalty into consideration.

From OpenGL 4.2, we can mediate between both sides by declaring the `gl_FragDepth` at the top of the fragment shader with a depth condition.

```glsl
layout (depth_<condition>) out float gl_FragDepth;
```

The condition can be:
- any: early depth testing is disabled
- greater: you can make the depth value larger than `gl_FragCoord.z`
- less: you can make the depth value smaller than `gl_FragCoord.z`
- unchanged: you will always write `gl_FragCoord.z`

```glsl
#version 420 core
out vec4 FragColor;
layout (depth_greater) out float gl_FragDepth

void main() {
    FragColor = vec4(1.0);
    gl_FragDepth = glFragCoord.z + 0.1;
}
```









