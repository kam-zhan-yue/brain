## Building an Executable

Usage of CMake revolves around one or more files named `CMakeLists.txt`. This will exist within any directory where we want to provide instructions to CMake on how to handle files and operations local to that directory or subdirectories. Each consists of a set of commands which describe some information or actions relevant to building the software.

The root CML should always start with:

```
cmake_minimum_required(VERSION 3.23)
project(ProjectName)
```

Then, we need to create a target. A target is the name the developer gives to a collection of properties.

The mechanisms of CMake are best understood as describing and manipulating targets and their properties. Targets are simply names, a handle to this collection of properties.

```
add_executable(MyProgram)
```

Now that we have a target, we can start associating properites with it like source files we want to build and link. The primary command is `target_sources()`

```
target_sources(MyProgram
	PRIVATE
		main.cxx
)
```


Each collection of files is prefixed by a scope keyword. When nothing depends on an executable, we set this to PRIVATE. This informs CMake that this property only belongs to MyProgram and is not inheritable.

Now, we make the files in a build folder
```
cmake -B build
```
- The -B flag tells CMake to use the given relative path as the location to generate files and store artifacts.
- If it is omitted, the current working directory is used. It is generally considered bad practice to do this

Then, we tell cmake to build the build folder.

```
cmake --build build
```

And we can run it with
```
./build/Tutorial
```

## Building a Library

```
add_library(MyLibrary)
```

Header files are required to build other parts of a given target. As such, header files are describe slightly differently than implementation files.

To describe a collection of header files, we use a `FILE_SET`.
```
target_sources(MyLibrary
	PRIVATE
		library_implementation.cxx
	PUBLIC
		FILE_SET myHeaders
		TYPE_HEADERS
		BASE_DIRS
			include
		FILES
			include/library_header.h
)
```

- The implementation file is a PRIVATE source
- The header file is a PUBLIC source
- A `FILE_SET` consists of the following parts:
	- `FILE_SET <name>` is the name of the file set
	- TYPE is the kind of files we are descibing
	- BASE_DIRS is the base locations for the files
	- FILES is the list of files

## Linking Libraries and Executables

```
target_link_libraries(MyProgram
	PRIVATE
		MyLibrary
)
```

There are three scope keywords
- PRIVATE (also called a non-interface property) is only available to the target which owns it. E.g. PRIVATE headers will only be visible to the target they're attached to
- INTERFACE is only available to targets which link the owning target. The owning target does not have access to the properties. A header-only library is an example of a collection of INTERFACE properties, as header-only libraries do not build anything themselves and do not need to access their own files
- PUBLIC is not a distinct property, but is a union of the PRIVATE and INTERFACE properties

The target has two specific properties, HEADER_SETS and INTERFACE_HEADER_SETS.

We can call `target_link_libraries` prior to defining the library with `add_library`. We just record the fact that the target has a dependency to a certain library.

### Neovim Bonus
In order to get this working in neovim, we need to generate a compile_commands.json file. We can add this command

```
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
```

This may generate a `compile_commands.json` in a file unreachable by the LSP. The LSP may not be configured to check the build folder

```lua
local lspconfig = require("lspconfig")
	lspconfig.clangd.setup({
	cmd = { "clangd", "--compile-commands-dir=build" },
})
```

## Subdirectories

```
add_subdirectory(SubdirectoryName)
```

Allows us to incorporate CMLs located in subdirectories of the project. When a `CMakeLists.txt` in a subdirectory is being processed by CMake, all relative paths described in the subdirectory CML are relative to that subdirectory.


```
cmake --build build --clean-first
```

is necessary to clean the original build directory prior to rebuilding.