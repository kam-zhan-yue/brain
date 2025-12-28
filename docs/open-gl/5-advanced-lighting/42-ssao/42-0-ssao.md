Ambient lighting is a fixed light constant we add to the overall lighting of a scene to stimulate the scattering of light. In reality, light scatters in all kinds of directions with varying intensities, so the indirectly lit parts of a scene should also have varying intensities.

One type of indirect lighting approximation is called ambient occlusion that tries to approximate indirect lighting by darkening creases, holes, and surfaces that are close to each other. These areas are largely occluded by surrounding geometry and thus light rays have fewer places to escape to, hence the areas appear darker. 

![[42-0-ssao.png]]

The image with ambient occlusion enabled does feel more realistic due to small occlusion-like details, giving the scene a greater feel of depth.

Ambient occlusion techniques are expensive as they have to take surrounding geometry into account. One could shoot a large number of rays for each point in space to determine its amount of occlusion, but that quickly becomes computationally infeasible for real-time solutions.

## SSAO
Screen-space ambient occlusion (SSAO) was published in 2007 and was used in Crysis. The technique uses a scene's depth buffer in screen-space to determine the amount of occlusion instead of real geometrical data. This approach is incredibly fast compared to real ambient occlusion and gives plausible results.

For each fragment on a screen-filled quad, we calculate an occlusion factor based on the fragment's surrounding depth values. The occlusion factor is then used to reduce or nullify the fragment's ambient lighting component. The occlusion factor is obtained by taking multiple depth samples in a sphere sample kernel surrounding the fragment position and comparing each of the samples with the current fragment's depth value. The number of samples that have a higher depth value than the fragment's depth represents the occlusion factor.

![[42-0-sphere-kernel.png]]

Each of the grey samples in the geometry contribute to the total occlusion factor; the more samples we find inside t geometry, the less ambient lighting the fragment should eventually receive.

The quality and precision of the effect directly relates to the number of surrounding samples we take. If the sample count is too low, the precision drastically reduces and we get an artifact called banding; if it is too high, we lose performance. By randomly rotating the sample kernel each fragment, we can get high quality results with a much smaller amount of samples. This comes at a price as the randomness introduces a noticeable noise pattern that we fix by blurring the results. 

![[42-0-banding.png]]

Above shows the effect of banding and randomness on the results.

Because the sample kernel used was a sphere, it caused walls to look gray as half of the kernel samples end up being in the surrounding geometry. Hence, we can use a hemisphere sample kernel oriented along a surface's normal vector.

![[42-0-hemiphere-kernel.png]]

By sampling a normal-oriented hemisphere, we do not consider the fragment's underlying geometry to be a contribution to the occlusion factor, removing the grey-feel of ambient occlusion and generally producing more realistic results.