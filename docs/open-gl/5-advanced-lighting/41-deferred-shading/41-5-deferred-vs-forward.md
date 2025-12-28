By itself (without light volumes), deferred shading is a nice optimisation as each pixel only runs a single fragment shader, compared to forward rendering where we'd often run the fragment shader multiple times per pixel.

Deferred rendering has notable disadvantages such as:
- A large memory overhead
- No MSAA
- Blending still needs to be done with forward rendering

When you have a small scene and not too many lights, deferred rendering is not necessarily faster and sometimes even slower as the overhead then outweighs the benefits of deferred rendering.

In more complex scenes, deferred rendering quickly becomes a significant optimisation; especially with the more advanced optimisation extensions. In addition, some render effects become cheaper on a deferred render pipeline as a lot of scene inputs are already available from the g-buffer.

Basically all effects that can be accomplished with forward rendering can also be implemented in a deferred rendering context; this often requires a small translation step.

For example, if we want to use normal mapping in a deferred renderer, we'd change the geometry pass shaders to output a world-space normal extracted from a normal map (using a TBN matrix) instead of the surface normal. The lighting calculations in the lighting pass don't need to be changed at all.

If you want parallax mapping to work, you'd want to first displace the texture coordinates in the geometry pass before sampling an object's diffuse, specular, and normal textures.