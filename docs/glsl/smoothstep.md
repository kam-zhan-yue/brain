Smoothstep depends on three parameters:
- The input x
- The left edge
- The right edge

The function receives a real number as an argument. It returns 0 if x is less or equal to the left edge and 1 if x is greater than or equal to the right edge. Otherwise, it smoothly interpolates, using Hermite interpolations and returns a value between 0 and 1.

The slope of the smoothstep function is zero at both edges. This is convenient for creating a sequence of transitions using smoothstep to interpolate each segment as an alternative to using more sophisticated or expensive interpolation techniques