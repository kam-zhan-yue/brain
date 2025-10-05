GLFW is a library, written in C, specifically targeted at OpenGL. GLFW gives us the bare extensions required for rendering goodies to the screen. It allows us to create an OpenGL context, define window parameters, and handle user input.

## Building GLFW
GLFW can be obtained from the webpage https://www.glfw.org/download.html. We can compile GLFW ourselves from the source code. This is to give a feel for the process of compiling open-source libraries as not every library will have pre-compiled binaries available. 

Once you've downloaded the source package, extract it and open its contents. We will use the following:
- The resultant library from compilation
- The **include** folder

Compiling the library from the source code guarantees that the resulting library is perfectly tailored for your your CPU/OS, a luxury pre-compiled binaries won't always provide. The problem with providing source code to the open world is that not everyone uses the same IDE or build system, which means the project/solution files provided may not be compatible with other people's setup. So, people then have to setup their own project/solution with the given .c/.cpp and .h/.hpp files, which is cumbersome.

### CMake
CMake is a tool that can generate project/solution files of the user's choice from a collection of source code files using pre-defined CMake scripts. This allows you to generate project files (e.g. for Visual Studio) from GLFW's source package, which you can use to compile the library. 

^ I will not be doing the above and just do a `brew install glfw` as I want this to be automated in the future.