OpenGL allows us to modify the comparison operators it uses for the depth test. We can set the comparison operator by calling `glDepthFunc`.
```c++
glDepthFunc(GL_LESS);
```

There are several comparison operators that can be used.

By default, `GL_LESS` is used that discards all fragments that have a depth value higher than or equal to the current depth buffer's value.