To import a model and translate it to our own structure, we include the following header files:

```c++
#include <assimp/Importer.hpp>
#include <assimp/scene.h>
#include <assimp/postprocess.h>
```

```c++
  void loadModel(string path) {
    // 1. Declare an Importer and call its ReadFile function
    Assimp::Importer import;
    const aiScene *scene = import.ReadFile(path, aiProcess_Triangulate | aiProcess_FlipUVs);

    // 2. After loading the model, check if the scene and the root node are not null
    if (!scene || scene->mFlags & AI_SCENE_FLAGS_INCOMPLETE || !scene->mRootNode) {
      std::cout << "ERROR::ASSIMP::" << import.GetErrorString() << std::endl;
      return;
    }

    // 3. Set the directory and process the root node, recursively
    directory = path.substr(0, path.find_last_of('/'));

    processNode(scene->mRootNode, scene);
  }
```


