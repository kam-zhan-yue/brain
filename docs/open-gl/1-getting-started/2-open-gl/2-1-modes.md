In the old days, using OpenGL meant developing in **immediate mode** (often referred to as the fixed function pipeline). The functionality of OpenGL was hidden within the library and developers didn't have much control over how OpenGL did its calculations. Developers wanted more control over their graphics.

The immediate mode is really easy to use and understand, but it is also extremely inefficient. For that reason, specification started to deprecate immediate mode functionality from version 3.2 onwards and started motivating developers to develop in OpenGL's core-profile mode.

When using OpenGL's core-profile, OpenGL forces us to use modern practices. When we try to use one of OpenGL's deprecated functions, OpenGL raises an error and stops drawing. The advantage of learning the modern approach is that it is very flexible and efficient. However, it's also more difficult to learn.

When using functionality from the most recent version of OpenGL, only the most modern graphics cards will be able to run your application. This is often why developers generally target lower versions of OpenGL and optionally enable higher version functionality.