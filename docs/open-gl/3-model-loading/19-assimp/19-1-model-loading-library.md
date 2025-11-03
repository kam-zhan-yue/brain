Assimp stands for *Open Asset Import Library*. It is able to import dozens of different model file formats by loading all the model's data into Assimp's generalised data structures. As soon as Assimp has loaded the model, we can retrieve all the data we need from Assimp's data structures.

When importing a model via Assimp, it loads the entire model into a *scene* object that contains all the data of the imported model/scene. Assimp then has a collection of nodes where each node contains indices to data stored in the scene object where each node can have any number of children.

![[19-1-assimp-tree.png]]

- All of the data of the scene/model is contained in the Scene object. It also contains a reference to the root node of the scene.
- The Root node of the scene may contain children nodes and could have a set of indices that point to mesh data in the scene object's `mMeshes` array. The scene's `mMeshes` array contains the actual Mesh objects, the values in the `mMeshes` array of a node are only indices for the scene's meshes array.
- A Mesh object itself contains all the relevant data required for rendering, such as vertex positions, normal vectors, texture coordinates, faces, and the material of the object.
- A Mesh contains several faces.
- A Face represents a primitive of the object (triangles, squares, points). A face contains the indices of the vertices that form a primitive. Because the vertices and the indices are separated, this makes it easy for us to render via an index buffer.
- Finally a mesh also links to a Material object that hosts several functions to retrieve the material properties of an object. Think of colours and/or texture maps (like diffuse and specular maps).

What we want to do is: first load an object into a Scene object, recursively retrieve the corresponding Mesh objects from each of the nodes, and process each Mesh object to retrieve the vertex data, indices, and its material properties. The result is then a collection of mesh data that we want to contain in a single `Model` object.