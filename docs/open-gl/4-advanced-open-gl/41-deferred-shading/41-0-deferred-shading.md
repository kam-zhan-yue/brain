Forward rendering refers to a straightforward approach where we render an object and light it according to all light sources in a scene. This is done for every object individually in the scene/. This is heavy on performance as each rendered object has to iterate over each light source for every rendered fragment, which is a lot. Forward rendering also tends to waste a lot of fragment shader runs in scenes with a high depth complexity as fragment shader outputs are overwritten.

Deferred shading aims to overcome these issues by drastically changing the way we render objects. It is based on the idea that we defer or postpone most of the heavy rendering to a later stage. Deferred rendering consists of two passes: 
- The first pass (geometry pass) renders the scene once and retrieves all kinds of geometrical information from the objects that we store in a collection of textures called the G-buffer. This includes position vectors, colour vectors, normal vectors, and/or specular values.
- The second pass (lighting pass) renders a screen-filled quad and calculates the scene's lighting for each fragment using the geometrical information stored in the G-buffer. Instead of taking each object all the way from the vertex shader to the fragment shader, we decouple its advanced fragment processes to a later stage. The lighting calculations are exactly the same, but we take all required input variables from the corresponding G-buffer textures, instead of the vertex shader.

![[41-0-deferred-shading.png]]

A major advantage of this approach is that whatever fragment ends up in the G-buffer is the actual fragment information that ends up as a screen pixel as it has passed the depth test. Deferred rendering opens up the possibility for further optimisations that allow us to render a much larger amount of light sources compared to forward rendering.

It comes with some disadvantages as the G-buffer requires us to store a large amount of scene data in texture colour buffers. Another disadvantage is that it doesn't support blending and MSAA no longer works. There are several workarounds for this.

Filling in the G-buffer isn't too expensive as we directly store object information such as position, colour, or normals into a framebuffer with small or zero amount of processing. Multiple render targets can achieve this in a single render pass.

