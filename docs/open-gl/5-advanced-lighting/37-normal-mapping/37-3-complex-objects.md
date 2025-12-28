Having to manually calculate these tangent and bitangent vectors is not something we do often. Assimp has a useful configuration bit we can set when loading a model called `aiProcess_CalcTangentSpace`. When this bit is supplied, Assimp calculates smooth tangent and bitangent vectors for each of the loaded vertices, similarly to how we did it.

```c++
const aiScene *scene = importer.ReadFile(
             path, aiProcess_Triangulate | aiProcess_FlipUVs |
             aiProcess_CalcTangentSpace
);
```

We can retrieve the calculated tangents with 
```c++
vector.x = mesh->mTangents[i].x;
vector.y = mesh->mTangents[i].y;
vector.z = mesh->mTangents[i].z;
vertex.Tangent = vector;
```

We will have to update the model loader to also load normal maps from textured model. The wavefront object (.obj) exports normal maps slightly different to Assimp's conventions.

```c++
   vector<Texture> normalMaps = loadMaterialTextures(material,
                                   aiTextureType_HEIGHT, "texture_normal");
```

> This is different for each type of loaded model and file format

With normal mapping, we can get the same level of detail on a mesh using a lot less vertices. The details on a high-vertex mesh and a low-vertex mesh with normal mapping are almost indistinguishable. 

