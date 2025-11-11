---
id: 29-4-interface-blocks
aliases: []
tags: []
---

Everytime we send data from the vertex to the fragment shader, we declare matching input/output variables. To organise these variables, GLSL offers interface blocks that allow us to group variables together.

```glsl
out VS_OUT {
    vec2 TexCoords;
} vs_out;

void main() {
    vs_out.TexCoords = aTexCoords;
}
```

We declare an interface block `vs_out` that groups together all the output variables we want to send to the fragment shader. This is useful when we want to group shader input/output into arrays.

The fragment shader looks like:
```glsl
in VS_OUT {
    vec2 TexCoords;
} fs_in;

void main() {
    FragColor = texture(texture, fs_in.TexCoords);
}
```

As long as both interface block names are equal, their correponding input and output is matched.
