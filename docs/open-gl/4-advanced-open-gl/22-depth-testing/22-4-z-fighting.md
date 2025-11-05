A common visual artifact may occur when two planes or triangles are closely aligned to each other than the depth buffer does not have enough precision to figure out which one of the two shapes is in front of the other. The result is that the two shapes continually seem to switch order.

This is known as **z-fighting** because it looks like the shapes are fighting over who gets on top.

### Prevent z-fighting
The first and most important trick is to *never place objects too close to each other in a way that some of their triangles closely overlap*. By creating a small offset between two objects, you can completely remove z-fighting between the two objects. In the case of the containers and the plane, we can move the containers slightly upwards in the positive y-direction.

A second trick is to *set the near plane as far as possible*. The precision is extremely large when close to the near plane. So if we move the *near* plane away from the viewer, we'll have significantly greater precision over the entire frustum range. However, this would cause clipping of near objects.

Another great trick is to use a *higher precision depth buffer*. Most depth buffers have a precision of 24 bits, but most GPUs can support 32 bit depth buffers.