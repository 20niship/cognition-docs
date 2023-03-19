
# Rendering

- Renderingする
- 結果はテクスチャとして使用、mu::Imageとして取得、Windowに描画できる

```cpp
// 
void render_to_texture(){
    mu::db::Object obj = Load("sample.ply");
    mu::Render rnd;
    rnd.add(obj);
    rnd.render();
    auto image = rnd.get_result();
}

// ui
void render_ui(){
    
}
```



- 1つのObjectにつき1つのマテリアル
- Object一つが複数のMeshを持つ
- Mesh1つにつきDrawCallを複数持つ（テクスチャが変わったときなど）

```
Object 
  - Material --> One Shader
  - Mesh 1 --> One VBO and VAO
  - Mesh 2
  - Mesh 3
  - DrawList * --> DrawList
  	 - RenderPass
  	 - Material
  	 - DrawCall 1(Mesh 1 part, texture 1)
  	 - DrawCall 2(Mesh 2 part, texture 2)
  	 - DrawCall 3(Mesh 1 part, texture 3)
```

- DrawList, Shader, Mesh(VBO)は使いまわすことがあるので、Engine側でnew / deleteし、ポインタのみを保持する
- 



