OpenAIとは
---------------------------------------------------------------------

強化学習の環境として広く使われているベンチマーク。

簡単にセットアップして手法を試すことができるのが特徴。

Box2DやMujocoなどの物理シミュレータ、atariやDoomなどのゲーム環境と連携している。

公式ドキュメント

*   [https://gym.openai.com/](https://gym.openai.com/)

Git Repository兼チュートリアル

*   [https://github.com/openai/gym](https://github.com/openai/gym)



インストール(Ubuntu14.04)
---------------------------------------------------------------------------------------------------------------------



```sh
  sudo apt-get install -y python-numpy python-dev cmake zlib1g-dev libjpeg-dev xvfb libav-tools xorg-dev python-opengl libboost-all-dev libsdl2-dev
```


### swigは公式HPから最新版を落としてくる(バージョンが古いとBox2Dのセットアップ関係で問題になる)
[http://www.swig.org/download.html](http://www.swig.org/download.html)から最新版のgzファイルをダウンロード
```
  tar -zxvf swig-xx.tar.gz
```
INSTALLのドキュメントに従ってインストール
```sh
  cd swig-xx
  ./configure
  make
  sudo make install
```


### OpenAI gymのインストール
```sh
  git clone https://github.com/openai/gym.git
  cd gym
  pip install -e .
```
これで最小構成は完了

後は使用したい環境を個別に
```sh
  pip install -e .'\[atari\]'
```
などのようにインストールする。



### rllab
カリフォルニア大学のチームが作ったベンチマーク [https://github.com/openai/rllab.git](https://github.com/openai/rllab.git)

Theanoをベースに以下のアルゴリズムが実装されている

*   REINFORCE
*   Truncated Natural Policy Gradient
*   Reward-Weighted Regression
*   Relative Entropy Policy Search
*   Trust Region Policy Optimization
*   Cross Entropy Method
*   Covariance Matrix Adaption Evolution Strategy
*   Deep Deterministic Policy Gradient

ドキュメント [https://rllab.readthedocs.io/en/latest/](https://rllab.readthedocs.io/en/latest/)

ソースを入手 `  git clone https://github.com/openai/rllab.git`



### ~/.bashrcに以下を追記
  export PYTHONPATH=(rllabのディレクトリ):$PYTHONPATH

インストールはこれで完了。後は必要に応じて依存パッケージを導入

Gitのmaster branchはpython3用に書かれているが、py2 branchはpython2.7用に書かれている。



### 依存関係
Theanoのインストールは別ページに譲る
```sh
  sudo apt-get build-dep python-pygame
  ./scripts/setup_linux.sh
```


使い方----------------------------------------------------------------



### 学習結果を再生する
以下を参照

[https://github.com/openai/rllab/issues/25](https://github.com/openai/rllab/issues/25)