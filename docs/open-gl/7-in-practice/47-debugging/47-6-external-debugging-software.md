There are third party applications that inject themselves in OpenGL drivers and can intercept all kinds of OpenGL calls, giving a large array of interesting data.

### RenderDoc
A great standalone debugging tool that can capture frames of an executable's current state. Within the captured frame(s), you can view the pipeline state, all OpenGL commands, buffer storage, and textures in use.

### CodeXL
A GPU debugging tool that gives a good set of information for profiling graphics applications. It works on NVIDIA and Intel cards, but without support for OpenCL debugging.

### NVIDIA Nsight
The Nsight plugin gives a large host of run-time statistics regarding GPU usage and frame-by-frame GPU state. It renders an overlay GUI system from within your application that you can use to gather all kinds of information, both at runtime and frame-by-frame analysis. However, it only works on NVIDIA cards.zn