`stb_image_h` is a populate single header image loading library. We can import this file from the Github repository and create an additional C++ file with the following code:

```c++
#define STB_IMAGE_IMPLEMENTATION
#include "stb_image.h"
```

By defining `STB_IMAGE_IMPLEMENTATION`, the preprocessor modifies the header file such that it only contains the relevant definition source code, effectively turning the header file into a `.cpp` file. 

We can load images using:
```c++
int width, heigth, nrChannels;
unsigned char *data = stbi_load("container.jpg", &width, &height, &nrChannels, 0);
```

The function first takes input as location of an image file. It then expects three `int`s as its second, third, and fourth argument that `stb_image.h` will fill with the resulting width, height, and number of colour channels. We need the image's width and height for generating textures later on.