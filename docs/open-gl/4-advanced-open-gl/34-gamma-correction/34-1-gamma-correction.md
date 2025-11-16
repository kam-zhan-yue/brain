Gamma correction applies the inverse of the monitor's gamma to the final output colour before displaying to the monitor. We multiply each of the linear output colours by an inverse gamma curve (making them brighter) and as soon as the colours are displayed by the monitor, the monitor's gamma curve is applied and the resulting colours become linear.

> Gamma correction brightens the intermediate colours so that as soon as the monitor darkens them, it all balances out.

### Example
Say we have the dark-red (0.5, 0, 0).
- Before displaying to the monitor, we apply the gamma correction to the colour value. We scale the colours to a power of 1/2.2. This becomes around (0.73, 0, 0)
- The corrected colours are then fed to the monitor and this is raised to a power of 2.2, becoming (0.5, 0, 0).
- The monitor finally displays the colours as we linearly set them in the application.

> The gamma of 2.2 is called the sRGB colour space. Since each monitor has its own gamma curve, games often allow players to change the game's gamma settings.

There are two ways to apply gamma correction:
- OpenGL's built-in sRGB framebuffer support
- Gamma correction through fragment shader(s)

## OpenGL
This open is the easiest, but we have the least control.
```c++
glEnable(GL_FRAMEBUFFER_SRGB);
```

Will tell OpenGL that each subsequent drawing command should first gamme correct colours (from the sRGB colour space) before storing them in colour buffers. The sRGB is a colour space that roughly corresponds to a gamma of 2.2.

## Fragment Shaders
We can apply gamma correction at the end of each relevant fragment shader run so that the final colours end up gamma corrected before being sent out to the monitor.

```glsl
void main() {
	float gamma = 2.2;
	FragColor.rgb = pow(fragColor.rgb, vec3(1.0/gamma));
}
```

To do this, we have to be consistent in applying gamma correction to each fragment shader that contributes to the final output. We can also introduce a post-processing stage in the render loop and apply gamma correction on the post-processed quad as a final step.