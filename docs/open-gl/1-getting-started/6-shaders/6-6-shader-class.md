Writing, compiling, and managing shaders can be cumbersome, so we can create a class that reads shaders from disk, compiles them, and links them, checks for errors, and is easy to use.

Create the shader class in a header file.
```c++
#ifndef SHADER_H
#define SHADER_H

#include <glad/glad.h>

#include <string>
#include <fstream>
#include <sstream>
#include <iostream>

class Shader {
public:
	// the program ID
	unsigned int ID:
	
	// constructor that reads and builds the shader
	Shader(const char* vertexPath, const char* fragmentPath);
	// use / activate the shader
	void use();
	// utility uniform functions
	void setBool(const std::string &name, bool value) const;
	void setInt(const std::string &name, int value) const;
	void setFloat(const std::string &name, float value) const;
}
#endif
```

The shader class holds the ID of the shader program. Its construct requires the file path of the source code of the vertex and fragment shader respectively so that we can store on a disk as simple text files.
