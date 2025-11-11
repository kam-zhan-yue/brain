Right now, we've rendered the skybox first before all other objects in the scene. This works but it is not efficient. If we render the skybox first, we're running the fragment shader for each pixel on the screen even though only a small part of the skybox will eventually be visible. 

For a performance boost, we can render the skybox last. This way, the depth buffer is completely filled with all the scene's depth values so we only have to render the skybox's fragments wherever the early depth test passes.

The problem is that the skybox will most likely render on top of all other objects since it is a 1x1x1 cube and passes most depth tests. Simply rendering it without depth testing is not a solution since the skybox will overwrite all the other objects in the scene as it's rendered last. We need to trick the depth buffer into believing that the skybox has a maximum depth value of `1.0` so that it fails the depth test whenever there's an object in front of it.

Perspective division is performed after the vertex shader has run, dividing the `gl_Position`'s `xyz` coordinates by its `w` coordinate. The `z` coordinate of the resultant division is equal to that vertex's depth value. Using this information, we can set the `z` component of the output position equal to its `w` component which will result in a `z` component that is always equal to 1.0. This is because when the perspective division is applied, the z component translates to `w/w = 1.0`

```glsl
void main() {
	TexCoords = aPos;
	vec4 pos = projection * view * vec4(aPos, 1.0);
	gl_Position  = pos.xyww;
}
```

> The above tricks OpenGL into believing that the skybox has a `z` of 1.0. Perspective Division is applied after the vertex shader step in order to turn the vec4 into normalised device coordinates. This is achieved by dividing by `w`. A depth value of 1.0 means the skybox is furthest away.

The resulting normalised device coordinates will then always have a `z` value equal to `1.0`: the maximum depth value. The skybox will as a result only be rendered wherever there are no objects visible. 

We have to change the function to `GL_LEQUAL` instead of the default `GL_LESS`. The depth buffer will be filled by 1.0 for the skybox, so we need to make sure the skybox passes the depth tests with values less than or equal to the depth buffer instead of less than.