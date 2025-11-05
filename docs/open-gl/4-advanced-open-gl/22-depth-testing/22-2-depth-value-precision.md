The depth buffer contains depth values between 0.0 and 1.0 and it compares its content with the z-values of all objects in the scene as seen from the viewer. These z-values in view space can be any value between the projection-frustum's near and far plane. We thus need some way to transform these view-space z-values to the range of [0, 1] and one way is to linearly transform them.
$$
F_{depth} = \frac{z - near}{far - near}
$$
Here, *near* and *far* are the values we used to provide to the projection matrix to set the visible frustum. The equation takes a depth value z within the frustum and transforms it to the range [0, 1].

![[22-2-graph.png]]

In practice, a linear depth buffer is never used. Because of projection properties, a non-linear depth equation is used that is proportional to 1/z. The result is that we get enormous precision when z is small and must less precision when z is far away.
$$
F_{depth} = \frac{1/z - 1/near}{1/far - 1/near}
$$
![[22-2-non-linear-graph.png]]

The values in the depth buffer are not linear in clip space. However, they are linear in view-space before the projection matrix is applied. A value of 0.5 in the depth buffer does not mean the pixel's z-value is halfway in the frustum.