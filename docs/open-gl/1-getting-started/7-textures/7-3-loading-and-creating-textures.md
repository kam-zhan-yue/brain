Texture images can be stores in dozen of file formats, each with their own structure and ordering of data. In order to render these, we can choose a file format we'd like to use (e.g. `.PNG`) and write our own image loader to convert the image format into a large array of bytes.

Another solution would be to use an image-loading library that supports several formats, such as `stb_image_h`