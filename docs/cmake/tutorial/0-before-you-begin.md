There are many possible ways we can run CMake depending on which generator we want to use for the build.

Take the `CMakeLists.txt`

```
cmake_minimum_required(VERSION 3.23)

project(Tutorial)

add_executable(hello)
target_sources(hello
  PRIVATE
    HelloWorld.cxx
)
```

```shell
cmake -B build
```

will generate a build system using the default generator for the platform.

```shell
cmake -G Ninja -B build
```

will use the `Ninja` generator instead of the platform default.

A multi-configuration generator will want to specify the build generation. The result of the build will be stored in a configuration-specific subdirectory.

```shell
cmake --build build --config Debug
./build/Debug/hello
```
