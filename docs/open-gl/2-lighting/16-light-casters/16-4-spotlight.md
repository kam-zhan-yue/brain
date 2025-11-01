A spotlight is a light source that is located somewhere in the environment that, instead of shooting light rays in all directions, only shoots them in a specific direction. The result is that only the objects within a certain radius of the spotlight's direction are lit and everything else stays dark. A good example of a spotlight would be a street lamp or a flashlight.

A spotlight in OpenGL is represented by a world-space position, a direction, and a cutoff angle that specifies the radius of the spotlight. For each fragment we calculate if the fragment is between the spotlight's cutoff directions. If so, we lit the fragment accordingly.

![[16-4-spotlight.png]]
- LightDir: the vector pointing from the fragment to the light source
- SpotDir: the direction the spotlight is aiming at
- Phi: the cutoff angle that specifies the spotlight's radius. Everything outside this angle is not lit by the spotlight
- Theta: the angle between the LightDir vector and the SpotDir vector. The theta value should be smaller than the Phi vector to be in the spotlight.

All we need to do is calculate the dot product between the LightDir and the SpotDir and compare this with the cutoff angle.
