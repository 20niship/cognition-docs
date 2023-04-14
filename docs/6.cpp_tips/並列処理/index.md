# 並列化とは


## どこで並列化するか？

- CPU 
  - std::thread →マルチスレッドによる並列化
  - OpenMP
- GPU
  - OpenGL →グラフィックライブラリ。グラフィック用のパイプラインを使ったりCompute Shaderを使って任意の計算を並列実行できる
  - OpenCL → OpenGLのコンピュートシェーダの機能を抜き出したようなもの
  - CUDA → NVIDIAのGPUで使えるGPU用の言語。OpenCVとかに便利なラッパが提供されているので使うと楽
  - Vulkan → 最近できた新しいCGライブラリ


# 並列ソフトウェアを最適化するためのステップ

## 参考文献
- https://www.isus.jp/others/three-stages-for-optimizing/
