# 作業日誌（道路点群セグメンテーション）

### 思ってること

- トンネルとかの特殊な環境のらべるがあるデータセット少ないのでは？
- 良さそうなモデル
    - FKAーConv
    - Feature Geometric Net

### Segmentation

aaa

- PPNIV_2018_Xavier_Roynard
    - [https://hal.science/hal-01763469/file/PPNIV_2018_Xavier_Roynard.pdf](https://hal.science/hal-01763469/file/PPNIV_2018_Xavier_Roynard.pdf)
- PointNet++を使った例
    - [http://www.open3d.org/2019/01/16/on-point-clouds-semantic-segmentation/](http://www.open3d.org/2019/01/16/on-point-clouds-semantic-segmentation/)
    - https://github.com/isl-org/Open3D-PointNet2-Semantic3D
- SCF-Net
    - [https://github.com/leofansq/SCF-Net](https://github.com/leofansq/SCF-Net)
    - [https://paperswithcode.com/paper/scf-net-learning-spatial-contextual-features](https://paperswithcode.com/paper/scf-net-learning-spatial-contextual-features)
    - Semantic3Dで学習させた例
      
        ![Untitled](%E4%BD%9C%E6%A5%AD%E6%97%A5%E8%AA%8C%EF%BC%88%E9%81%93%E8%B7%AF%E7%82%B9%E7%BE%A4%E3%82%BB%E3%82%AF%E3%82%99%E3%83%A1%E3%83%B3%E3%83%86%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%EF%BC%89%2008f15d5085504a2c949dea0730b55cdb/Untitled.png)
        
    - MIT
- Feature Geometric Net
    - [https://paperswithcode.com/paper/fg-net-fast-large-scale-lidar-point](https://paperswithcode.com/paper/fg-net-fast-large-scale-lidar-point)
    - https://github.com/KangchengLiu/Feature-Geometric-Net-FG-Net
    - MIT　LICENSE
- FKA-Conv
    - [https://paperswithcode.com/paper/lightconvpoint-convolution-for-points](https://paperswithcode.com/paper/lightconvpoint-convolution-for-points)
    - config.yamlかえるだけで色んなデータセット学習できるっぽい。使いやすそう
    - PariLite3Dで最高スコアだしてる。
    - *Apache License, Version 2.0 (the "License");*

### dataset

- KITTI
    - リアルタイムの自動運転用のため、密度は結構疎らしい（[これ]([https://hal.science/hal-01763469/file/PPNIV_2018_Xavier_Roynard.pdf](https://hal.science/hal-01763469/file/PPNIV_2018_Xavier_Roynard.pdf)) )
    - それのセマンティックセグメンテーション用のSemanticKITTIがある
- NuScenes
- **Cityscapse**
    - 30クラスくらいの細かい分類がされている　[https://www.cityscapes-dataset.com/dataset-overview/](https://www.cityscapes-dataset.com/dataset-overview/)
- Semantic3D
    - [https://www.semantic3d.net/](https://www.semantic3d.net/)
    - **four billion** of labelled points.
    - [https://paperswithcode.com/dataset/semantic3d](https://paperswithcode.com/dataset/semantic3d)
    - 1: man-made terrain, 2: natural terrain, 3: high vegetation, 4: 
      low vegetation, 5: buildings, 6: hard scape, 7: scanning artefacts, 8: 
      cars
        - 人はいないという前提？
        - トンネルとか特殊な環境はなさそう
- Sensaturban
    - [https://paperswithcode.com/dataset/sensaturban](https://paperswithcode.com/dataset/sensaturban)
    - 写真測量点群データセット。特定の場所というより地域全体の点群
    - 点群ではあるがなんか違いそう（航空写真とレーダーから点群に復元してる？）
    
    ![Untitled](%E4%BD%9C%E6%A5%AD%E6%97%A5%E8%AA%8C%EF%BC%88%E9%81%93%E8%B7%AF%E7%82%B9%E7%BE%A4%E3%82%BB%E3%82%AF%E3%82%99%E3%83%A1%E3%83%B3%E3%83%86%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%EF%BC%89%2008f15d5085504a2c949dea0730b55cdb/Untitled%201.png)
    
- Paris Lite 3D
    - よさそう
    - [https://paperswithcode.com/sota/lidar-semantic-segmentation-on-paris-lille-3d](https://paperswithcode.com/sota/lidar-semantic-segmentation-on-paris-lille-3d)
    - 50 different classeだし良さそう

### 参考資料

- https://github.com/yanx27/2DPASS
- [https://www.sciencedirect.com/science/article/abs/pii/S0924271613000464](https://www.sciencedirect.com/science/article/abs/pii/S0924271613000464)
- Semantic3D
