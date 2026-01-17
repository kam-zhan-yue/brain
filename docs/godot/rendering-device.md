[See documentation here](https://docs.godotengine.org/en/stable/classes/class_renderingdevice.html#class-renderingdevice)

The `RenderingDevice` is an abstraction for working with modern low-level graphics APIs such as Vulkan. Compared to the `RenderingServer`, the `RenderingDevice` is much lower-level and allows working more directly with the underlying graphics APIs.

On startup, Godot creates a global `RenderingDevice` which can be retrieved using `RenderingServer.get_rendering_device()`. The global `RenderingDevice` performs drawing to the screen.

### Local Rendering Devices
Using `RenderingServer.create_local_rendering_device()` can create "secondary" rendering devices to perform drawing and GPU compute operations on separate threads.
