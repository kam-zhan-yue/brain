To render images with different levels of transparency, we need to enable blending.

```c++
glEnable(GL_BLEND);
```

Blending in OpenGL happens with the following equation:
$$
C_{result} = C_{source} * F_{source} + C_{destination} * F_{destination}
$$
- Csource: the source colour vector. This is the colour output of the fragment shader (the colour in front)
- Cdestination: the destination colour vector. This is the colour vector currently stored in the colour buffer. (the colour behind)
- Fsource: the source factor value. Sets the impact of the alpha value on the source colour
- Fdestination: the destination factor value. Sets the impact of the alpha value on the destination colour.

After the fragment shader has run, this blend equation is used on the fragment's colour output with whatever is currently in the colour buffer. The source and destination colours will be automatically set by OpenGL, but the source and destination factor can be set to a value of our choosing.

### Blend Example

![[24-2-blend-example-1.png]]

We have two squares where we want to draw the semi-transparent green on top of the red square. The red square is the destination colour (and is first in the buffer).

We want to multiply the green square with its colour value so we want to set Fsource to the alpha value of the source colour vector, which is `0.6`. Then, it makes sense that the destination colour have a contribution equal to the remaining alpha value. If the green square contributes 60% to the final colour, we want the red square to contribute 40% to the final colour.

So we set Fdestination equal to one minus the alpha value of the source colour vector.

$$
C_{result} = 
\left(
\begin{array}{cc}
0.0 \\
1.0 \\
0.0 \\
0.6 \\
\end{array}
\right)
* 0.6 +
\left(
\begin{array}{cc}
1.0 \\
0.0 \\
0.0 \\
1.0 \\
\end{array}
\right)
* (1 - 0.6)
$$
The result is that the combined square fragments contain a colour that is 60% green and 40% red.

![[24-2-blend-example-2.png]]

The resulting colour is then stored in the colour buffer, replacing the previous colour.

## `glBlendFunc`

To use this, we use `glBlendFunc(GLenum sfactor, GLenum dfactor)`

The constant colour can be set separately via the `glBlendColor` function.

![[24-2-blend-table.png]]

The two square example would use the *alpha* of the source colour vector for the source factor and 1 - *alpha* of the same colour vector for the destination factor

```c++
glBlendFunc(GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA);
```

It is also possible to separate options for the RGB and alpha channel individually.

```c++
glBlendFuncSeparate(GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA, GL_ONE, GL_ZERO);
```

This function sets the RGB components but only sets the resulting alpha component be influenced.

OpenGL gives more flexibility by allowing us to change the operator between the source and destination part of the equation. Right now, they are added, but we can subtract them..

```c++
glBlendEquation(GLenum mode);
```