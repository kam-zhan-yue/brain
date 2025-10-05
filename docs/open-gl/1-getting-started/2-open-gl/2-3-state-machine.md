OpenGL itself is a large state machine: a collection of variables that define how OpenGL should currently operate. The state of OpenGL is commonly referred to as the OpenGL context.

When using OpenGL, we change its state by setting some options, manipulating some buffers and then render using the current context.

Whenever we tell OpenGL that we want to draw lines instead of triangles, we change the state of OpenGL, by changing some context variable that sets how OpenGL should draw. As soon as we change the context, the next drawing will reflect the change.

When working in OpenGL, there are several state-changing functions that change the context, and several state-using functions that perform some operations based on the current state of OepnGL.