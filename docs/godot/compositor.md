[See documentation here](https://docs.godotengine.org/en/stable/tutorials/rendering/compositor.html)

The compositor is a feature that allows control over the rendering pipeline when rendering the contents of a Viewport.

It can be configured on a World Environment node where it applies to all Viewports, or it can be configured on a Camera3D and apply only to the Viewport using that camera.

The Compositor resource is used to configure the compositor.

## Compositor Effects
Compositor effects allow you to insert additional logic into the rendering pipeline at various stages. As the core logic of the compositor effect is called from the rendering pipeline, it is important to note that this logic will thus run within the thread on which rendering takes place. Care needs to be taken to ensure we don't run into threading issues.


