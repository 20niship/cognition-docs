# 認識・経路探索・C++ドキュメント

- 以下のような内容を含んでいます：
  - 点群処理 : センサ類の基礎知識からセグメンテーション、フィッティングまで。ちょっと機械学習
  - 画像処理：いろいろ
  - 経路探索：RRT, MPC, 車両モデル
  - Computer Graphics：基礎知識と数学とOpenGLやVulkanについて、GLSLシェーダ芸とレイキャスティング
  - アーキテクチャ・デザイン
  - アルゴリズム：８分岐、最近某探索、凸法検出などの点群処理に必要な処理、実装
  - C++ Tips
    - 並列計算：GPUやCPUのマルチスレッドを使って並列計算する方法
    - C++のプロジェクトを作るときに最低限する必要があること
    - プログラムの処理速度の上げ方

## Commands

* `mkdocs new [dir-name]` - Create a new project.
* `mkdocs serve` - Start the live-reloading docs server.
* `mkdocs build` - Build the documentation site.
* `mkdocs -h` - Print help message and exit.

## Project layout

    mkdocs.yml    # The configuration file.
    docs/
        index.md  # The documentation homepage.
        ...       # Other markdown pages, images and other files.

!!! Note
    これはノートです。

!!! Tip
    ヒントです。

!!! Warning
    これは警告です
    
!!! Danger
    これは危険です。


-------------画像テスト---------

<!--![](media/hanekawa.png) -->

-------------画像テスト終わり---------
