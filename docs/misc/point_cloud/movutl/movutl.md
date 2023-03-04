# データ構造/ファイル構造

## namespace structure

```mermaid
graph LR;

subgraph DB
subgraph 2D
Effect --> Layer
Layer --> Composition
end
subgraph 3d
Bone --> Object
Light --> World
Shader --> Object
Mesh --> Object
Material --> Object
Object --> World
Camera --> World
end
BaseEffector --> Animator
Animator --> AnimatorRoot
Animator --> MeshAnimator
Animator --> IntAnimator
Animator --> CustomAnimator
BaseEffector --> Modifier
Modifier --> Array
Modifier --> Solid
Modifier --> ToMesh
end

subgraph render
Render
end

subgraph UI
Widget-->Button
Render-->window
Button -->Window
end

    
```



# Documentation以前

- `DataView`と`Data`に分かれている
  - `Data`には実際のデータがバイナリ形式で格納されており、`DataView`にはそのデータへのポインタと使い方がある
- `DataView`一つに付き、1つの`Data`オブジェクトが存在する

```cpp
class DataView{
    ID2{
        DataType type; // Scene, Object, World, Effect, Custom ...... (ここによってUIの挙動が変化する)
        uint64_t type2; // 
        int size_in_bytes;
    } id;
    Data *ptr;
	Keyframe *key;
    py_instance *inst; // if neccesary
};

class Data{
    Data() = default;
    ID id;
    char name[60];
}

class ObjectData : public Data{
	DataView *effects;
}

class SceneData : public Data{
	int width, int height;
    DataView *objects;
}

std::vector<DataView> scenes, world, objects, effects....;

```

