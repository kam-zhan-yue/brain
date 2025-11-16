## OpenGL's Rasteriser
The rasteriser is the combination of all algorithms and processes that sit between your final processed vertices and the fragment shader. The rasteriser takes all vertices belonging to a single primitive and transforms this to a set of fragments. Vertex coordinates can theoretically have any coordinate, but fragments can't since they are bound by the resolution of your screen. There will never be a one-to-one mapping between vertex coordinates and fragments, so the rasteriser has to determine in some way what fragment/screen-coordinate each specific vertex will end up at.

![[32-1-sample-point.png]]

In the grid of screen pixels, each pixel has a centre known as a sample point. This is used to determine if a pixel is covered by the triangle. The red sample points are covered by the triangle and a fragment will be generated for that covered pixel. Even though some parts of the triangle edges still enter screen pixels, the pixel's sample point is not covered by the inside, so the pixel is not included in the fragment shader.

The complete rendered version of the triangle would look like this:

![[32-1-rendered.png]]

Due to the limited amount of screen pixels, some pixels will be rendered along an edge and some won't. This gives rise to the jagged edges.

## Multisampling
Multisampling doesn't use a single sampling point, but multiple sampling points. For example, we can use 4 subsamples in a general pattern and use those to determine pixel coverage.

![[32-1-multisampling.png]]

> The amount of sample points can be any number we'd like with more samples giving us better coverage precision.

In the above image, we determined that 2 subsamples were covered by the triangle. The next step is to determine a colour for the pixel. We *could* run the fragment shader for each covered subsample and later average the colours of each subsample per pixel. But this would mean we run the fragment shader many times, drastically reducing performance.

MSAA works by having the fragment shader running once per pixel regardless of however many subsamples the triangle covers. The fragment shader runs with the vertex data interpolated to the centre of the pixel. MSAA then uses a much larger depth/stencil buffer to determine subsample coverage. The number of subsamples covered determines how much the pixel colour contributes to the framebuffer. Since only 2 of the 4 samples were covered, half of the triangle's colour is mixed with the framebuffer colour, resulting in a light blue-ish colour.

The result is a high resolution buffer where all the primitive edges now produce a smoother pattern.

![[32-1-multi-sample-points.png]]

This results in a blend, instead of a harsh cut-off.

![[32-1-multi-rendered.png]]

This causes the edge to appear smooth when viewed from a distance. Depth and stencil values are stored per subsample. Colours are also stored per subsample as well for the case of multiple triangles overlapping a single pixel. For depth testing, the vertex's depth value is interpolated to each subsample before running the depth test. For stencil testing, we store the stencil values per subsample.

This does mean that the size of the buffers are not increased by the amount of subsamples per pixel.