Getting a simple string to render on screen is not simple with OpenGL. It gets difficult when each character has a different width, height, and margin. Since there is no support for text capabilities within OpenGL, we need to define a system for rendering text to the screen.

There are no graphical primitives for text characters, we have ot get creative. Some techniques involve drawing letter shapes with GL_LINES, creating 3D meshes of letters, or render character textures to 2D quads in a 3D environment.

Most developers choose to render character textures onto quads. This shouldn't be too difficult, but getting the relevant character(s) onto a texture could prove challenging.

## Bitmap Fonts
In the early days, rendering text involved selecting a font, extracting out all relevant characters and placing them within a single large texture. Such a texture, known as a bitmap font, contains all character symbols we want to use in predefined regions of the texture. 

These character symbols of the fonts are known as glyphs. Each glyph has a specific region of texture coordinates around them. Whenever you want to render a character, you select the corresponding glyph by rendering this section of the bitmap font to a 2D quad.

This approach has several advantages and disadvatnages.
- It is relatively easy to implement and because bitmap fonts are pre-rasterised, they are quite efficient.
- However, it is not particularly flexible. When you want to use a different font, you need to recompile to a completely new bitmap font and the system is limited to a single resolution.

This approach was quite popular back in the day since it is fast and works on any platform, but more flexible approaches exists today, such as loading TrueType fonts using the FreeType library.