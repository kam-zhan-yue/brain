The current setup is a static skybox, but it doesn't include the 3D scene with possibly moving objects. If we had a mirror-like object with multiple surrounding objects, only the skybox would be visible in the mirror.

Using framebuffers, it is possible to create a texture of the scene with all 6 different angles from the object in question and store those in a cubemap in each frame. We can then use this to dynamically generate a cubemap to create realistic reflection and refractive surfaces that include all other objects.

This is called dynamic environment mapping because we dynamically create a cubemap of an object's surroundings and use that as its environment map.

The disadvantage is that we have to render the scene 6 times per object using an environment map. Modern applications try to use the skybox as much as possible and pre-render cubemaps wherever they can to create pseudo-environment maps. While dynamic environment mapping is a great technique, it requires a lot of clever tricks and hacks to get it working in an actual rendering application without too many performance drops.