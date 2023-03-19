# Plugin開発

## Overview

プラグインの種類にはいくつかあり、

- UIそのものを定義する
- エフェクト
- カスタムオブジェクト
- キーマップ変更
- UIからコマンドを実行する
- 関数定義

などである。





## MovFxの場合



### Blender的な書き方

```cpp
```

### AfterEffect的な書き方

```cpp

```



### データ管理



## Misc

```cpp
// ----   方法1
// プラグインのファイル

class 

class ObjectData{
    struct ID{
        uint32_t object_type; // データタイプごとに異なる値を取る。
        uint32_t custom_type; // ユーザー定義のオブジェクトについて異なる値を取る。このときobject_type == OBJECT_TYPE_USERDEINEDになる
        uint64_t instance_id  //インスタンスごとに任意の値が設定される。
	};
    ID; // IDはそれ全体でプロジェクト内で重複しない値となり、これでリンクすることができる。
    char *name[30];
    
    Data1 data1;
    Data2 data2;
    // .........
}

void UISetup(UI *ui, ObjectData *d){
}

void SetupUI(UI *ui, ObjectData *d){
    ui->setRenderer(custom_type, UISetup);        
}

SetupParams(){
    ui->Add<Data1>(d->data1);
    ui->Add<Data2>(d->data2);
    ui->AddRow()->Add({
        d->data3, d->data4, d->data5
    });
}

Render()
```

***目標＝RenderingとCallbackの動作をどちらか片方定義するだけでいいようにする***

```cpp
class Animation<T>{
    
}

class Effects{
    struct EffectWrapper{
        Effect *fx;
        Animation *ani;
    }
}


```





```cpp
//-----  muファイルに保存されるデータ  -----
class ProjectData{
  Setting project;
  
  // 2D data
  Compo   compos[];
  Image   images[];
  Video   videos[];
  Audio   audios[];
  Font    fonts[];
  Particle  particles[];
  Mask masks;
    
  // 3D data
  Mesh    meshes[];
  Light   lights[];
  Texture textures[];
  Material  materials;
  Collection collections;
  PhysicsWorld world;
  Speaker speakers;
  
  // muファイルに保存されるObjectの中で、ユーザー定義になりうるもの。
   Effects effects;
   Node nodes;
   CustomObject objects[];   
};

struct bContext{
  Main main;
    
//-----  muファイルに保存されず、起動時に読み込まれるデータ  -----
  // ロードされるもの
  Plugin  plugins[];
  Tasks tasks[];
  Sheduler scheduler;
  UI ui;
  Log log;
  History history;
}


class MainLoop(){
	PF_in in;
    PF_out out;
    for(auto &f : selected_object.fx_list) render(); 
    ui.analyze_inputs(); // ここで ImGUIIOのデータを元にそれぞれのWidgetでいろいろが実行される
    tasks.run(); // 複数のタスク（レンダリング、UI更新等）をここで実行
}
```



# データタイプ

これらすべてのデータに対して、アニメーションをつけることが可能である。
- データはアニメーションによって変化しうるし、アニメーションもデータによって変化しうる。
  - `Anim<float> f;  f.insert_keyframe(20, 100); `// データの変化

基本データ

- string
- int
- float
- int array
- float array
- ID
- Animation
- Project
- Layer
  - Camera
  - Light
  - Video
  - .......(custom object)
- Composition
  - World
  - Physics
- Effect
  - brightness
  - clipping,
  - .......(custom object)
- Script (UIからのコールバックによって任意の関数を実行する)
  - Renderer,
  - RAMプレビュー
  - .......(custom object)
- UI
  - button
  - slider
  - .....(custom object)




## Examples

### shelf

```cpp
Renderer render;
int main(){
    auto obj = new_obj(obj_file_name);
    obj.set(default_light);
    obj.set(default_material);
    render.add(obj);
    render.add(light);
    
    wnd = vkUI::initWindow();
    wnd.add(obj);
    
    while(1){
       cv::Mat img = render.render_offscreen();
	   process(img); 
       vkUI::update();
    }
}
```



### MovUtl

```cpp
int main(){
    Render renderer;
    DB db;
	Proc proc;
    while(1){
        vkUI::update();
        proc.update(vkUI::getImGuiIO());
    }
}
```



## 疑問点

### データ構造

- 任意の構造体とカスタムの構造体それぞれにおいて、外部から値の型について知り、値を参照・変更できる必要がある。


 ### Animatorと外部ライブラリのEffectorの値の受け渡しについて

- 通常時
- Arbitary data (任意構造のデータのとき)


