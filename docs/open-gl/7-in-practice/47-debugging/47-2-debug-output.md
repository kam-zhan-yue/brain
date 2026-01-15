A less common, but more useful tool is an OpenGL extension called **debug output** that became part of core OpenGL since version 4.3. With the debug output extension, OpenGL will directly send an error or warning message to the user with a lot more details compared to `glCheckError`. Not only does it provide more information, but it can also help you catch errors exactly where they occur by using a debugger.

In order to start using debug output, we have to request a debug output context form OpenGL at the initialisation process. This process varies on the windowing system you use.

## Debug output in GLFW
This is achieved with:

```c++
glfwWindowHint(GLFW_OPENGL_DEBUG_CONTEXT, true);
```

> Using OpenGl in debug context can be significantly slower compared to using a non-debug context.

To check if we have successfully initialised a debug context, we can query OpenGL:

```c++
int flags; glGetIntegerv(GL_CONTEXT_FLAGS, &flags);
if (flags & GL_CONTEXT_FLAG_DEBUG_BIT) {
	// initialise debug output
}
```

Couldn't get this to work, so skipping. I think I need to change my glad.h file, but I am not too interested.