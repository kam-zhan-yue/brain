Another useful trick for is displaying framebuffer's content(s) in some pre-defined region of your screen. You're likely to use framebuffers quite often and, as most of their magic happens behind the screen, it is sometimes different to figure out what's going on. Displaying the content(s) of a framebuffer is a useful trick to quickly see if things look correct.

> Displaying the contents (attachments) of a framebuffer as explained here only works on texture attachments, not render buffer objects.

Using a simple shader that only displays a texture, we can easily write a small helper function to quickly display any texture at the top-right of the screen.j