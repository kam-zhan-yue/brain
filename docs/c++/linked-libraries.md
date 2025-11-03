In order to get libraries like Assimp and GLFW to run in OpenGL. There are a few options like building from source and using FetchContent.

Building from source is a pretty hardcore way, so here's how to do it:

1. Add as a git submodule to an external folder
```shell
git submodule add https://... open-gl/external/assimp
git submodule update --init --recursive
```

2. Add it to your `CMakeLists.txt`
```shell
# Configure Assimp
set(ASSIMP_BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(ASSIMP_BUILD_ASSIMP_TOOLS OFF CACHE BOOL "" FORCE)
set(ASSIMP_INSTALL OFF CACHE BOOL "" FORCE)
set(ASSIMP_NO_EXPORT ON CACHE BOOL "" FORCE)
add_subdirectory(external/assimp)
```

[See a good example here](https://github.com/unsettledgames/debut-engine/blob/main/Debut/CMakeLists.txt)