Debugging in graphics program is difficult as most errors are visual. There is no console to output text to, no breakpoints in GLSL code, and no way of easily checking the state of GPU execution.

## `glGetError`

The moment you incorrectly use OpenGL (like configuring a buffer without first binding something), it will take notice and generate one or more user error flags behind the scenes. We can query these error flags using `glGetError` that checks the error flag(s) set and returns an error value if OpenGL got misused.

```c++
GLenum glGetError();
```

The moment `glGetError` is called, it returns either an error flag or no error at all. 

![[47-1-error-flags.png]]

Within OpenGL's function documentation, you can always find the error codes a function generates the moment it is incorrectly used.
- The moment an error flag is set, no other error flags will be reported
- The moment `glGetError` is called, it clears all error flags. This means if you call `glGetError` once at the end of each frame and it returns an error, you can't conclude that this was the only error, and the source of error could've been anywhere in the frame.

```c++
unsigned int tex;
glBindTexture(GL_TEXTURE_2D, tex);
cout << glGetError() << endl; // returns 1282 as the texture isn't generated yet

glTexImage2D(GL_TEXTURE_3D, 0, GL_RGB, 512, 512, 0, GL_RGB, GL_UNSIGNED_BYTE, nullptr);
cout << glGetError() << endl; // returns 1280 as it is an invalid enum

unsigned int *textures;
glGenTextures(-5, textures);
cout << glGetError() << endl; // returns 1281 as it is an invalid value

cout << glGetError() << endl; // returns 0
```

The great thing about `glGetError` is that it makes it relatively easy to pinpoint where any error may be and to validate the proper use of OpenGL. Let's say you get a black screen and you have no idea what is causing it: is the framebuffer not properly set? Did I forget to bind a texture?

By calling `glGetError` all over your codebase, you can quickly catch the first place an OpenGL error starts showing up.

By default `glGetError` only prints error numbers, which isn't easy to understand unless you memorised error codes. It often makes sense to make a helper function.

```c++
GLenum glCheckError_(const char *file, int line) {
  GLenum errorCode;
  while ((errorCode = glGetError()) != GL_NO_ERROR) {
    string error;
    switch (errorCode) {
      case GL_INVALID_ENUM: error = "INVALID_ENUM"; break;
      case GL_INVALID_VALUE: error = "INVALID_VALUE"; break;
      case GL_INVALID_OPERATION: error = "INVALID_OPERATION"; break;
      case GL_OUT_OF_MEMORY: error = "OUT_OF_MEMORY"; break;
      case GL_INVALID_FRAMEBUFFER_OPERATION: error = "INVALID_FRAMEBUFFER_OPERATION"; break;
    }
    cout << error << " | " << file << " (" << line << ")" << endl;
  }
  return errorCode;
}
#define glCheckError() glCheckError_(__FILE__, __LINE__)
```
- The preprocessor directives `__FILE__` and `__LINE__` are variables that get replaced at compile time with the respective file and line they were compiled in.

`glGetError` doesn't help you too much as the information it returns is rather simple, but it does often help to catch typos or quickly pinpoint where things went wrong.