# 点群について全集

# 入力データ（三次元データの作成方法）
様々なセンサを使って三次元のデータを取得することができる。


## 超音波センサ Sonar
- 物体に超音波を当てて反射音が帰ってくるまでの時間を測定します。 
- 測定距離：３〜５ｍくらいまで
- 特徴
  - O 安価
  - O 超音波なのでガラス等の光が透過するオブジェクトへの距離も図れる
  - ☓ 精度はくそ
  - ☓ 音なのでどうしても範囲が広がる。大きいオブジェクトしか距離を測れない
- [](https://akizukidenshi.com/img/goods/2/M-11009.jpg)

## 赤外線式測距センサ
- 赤外線LEDとPSD(position sensitive detector)を使用して、非接触で距離を検出することができます。
- 範囲：10 ~ 1mくらい
- ![](https://akizukidenshi.com/img/goods/3/I-02551.jpg) (https://akizukidenshi.com/catalog/g/gI-02551/)

## ToFセンサ（Time of Flight）
- 物体に光を当てて反射光が帰ってくるまでの時間を測定することで、ある一点までの距離を図ります
- ToFセンサの例：
  - https://akizukidenshi.com/catalog/g/gM-12590/
  - ![](https://akizukidenshi.com/img/goods/L/M-12590.JPG)
  - 特徴
    - 一点しか測れないが割と正確に図れる
    - 安価

## LiDAR（Light Detection and Ranging）
- レーザー光を使用して物体までの距離を測定する装置
  - ![](https://japan.cnet.com/storage/2019/09/12/ee9a00a62e60f3ee052736271936083c/2019_09_12_sato_nobuhiko_010_image_01.jpg)
    - LiDARで得られた点群を可視化し、物体のバウンディングボックスを表示したもの
    - LiDARはセンサを回転させて全方位の距離w測定するので、得られる点群は上画像のように円形になる
- レーザー光を物体に照射し、戻ってくるまでの時間を測定することで距離を算出する
- レーザー照射部と受光部がセンサ内部で回転することで、360度のパノラマ距離データを取得できる（ $(\theta, r)$のリストとして点群がえられる
  - ![](https://img.directindustry.com/ja/images_di/photo-mg/182407-10533276.jpg)
  - ![](https://xtech.nikkei.com/atcl/nxt/column/18/00009/00066/topm.jpg)
- 自動運転や地図作成などで広く使われている。
- 高価（１万円いかのおもちゃもあるが、ちゃんとしたものは数万円〜）
- １つの距離センサを回転させる2D LiDARと複数の測距センサを取り付けた3D LiDARがある
- 主に２つの距離測定方式が用いられる
  - ToF方式（Time of Flight方式）
    - ↑のToFセンサと同じ原理。パルス波を物体に照射して反射までの時間を図る
  - FMCW(Frequency Modulated Continuous Wave, 周波数連続変調方式)
    - ドップラー効果を利用することで速度も測る
    - 比較的高価、高精度

また、測定距離や使用用途に分かれて大きく5種類に分別される：

  - TLS（地上型3Dレーザー）
  - ULS（UAVレーザー, UAV＝Unmanned aerial vehicle＝無人航空機）
  - ALS（航空レーザー, ALS=Aerial laser scanning=有人航空機）
  - MMS（車載型レーザー, MMS=Mobile Mapping System）
  - MLS（モバイルレーザー, MLS=Mobile Laster Scanning）
- LSはLaser Scannerの略
- Laser Scanの分類にTOFとFMCWがある


## ミリ波レーダー

    ミリ波レーダー（Millimeter Wave Radar）
    ミリ波レーダーは、電磁波を使用して物体の距離や速度を測定する技術です。これにより、車両や人物などの動きを追跡し、三次元構成を作成することができます。ミリ波レーダーは、雨や霧、暗闇などの悪条件下でも正確な測定が可能であり、自動運転車や防犯カメラなどの分野で広く使用されています。

    SfM（Structure from Motion）
    SfMは、写真やビデオから物体の位置や形状を推定する技術です。これにより、建物や地形などの三次元構成を作成することができます。SfMは、複数の画像を使用するため、画像が存在すればどこでも使用できるという利点があります。また、ドローンやスマートフォンなどの手軽なデバイスでも利用できるため、広範な分野で活用されています。
![](https://haratta-tech-lab.com/wp-content/uploads/2020/11/20200908221657.jpg)


以上が一般的な「`実際に距離を測定する`」測定機器・センサです。
他にも、距離を測定しなくてもセンサから得られるデータをもとに距離を算出する方法です


## 単一画像からの深度推定 (Depth estimation)


## 複眼カメラの視差を使う
Zed
![](https://m.media-amazon.com/images/I/61QnQHliBFS._AC_SL1500_.jpg)


## 複数のRGB画像からのフォトグラメトリ
- SfM Structure from Motionが行われる

画像です。取得した3Dデータの生フォーマットは深度マップです。
深度センサーから直接抽出され、グレーで表現された
レベルの画像になります。デプスセンサーは、多くの場合、通常のRGB
カメラで色情報を追加提供します。自然な2Dグリッド
このRGB-D画像の構造により、3Dデータの高速処理が可能です。
画像シーケンス 物体や部屋をスキャンするとき、ユーザーは
は、携帯型センサーを周囲に移動させる。を獲得することが目標です。
できるだけ多くのビューを表示し、最も正確な再構成を構築します。
これにより、多くの2D画像が生成され、それを利用することができます。
されたり、後処理されたりする。一般的に、一連のRGB-D画像は
を表す2.5次元の色付き点群列を与える。
同じ項目は、1つの3Dポイントに統合されます。
をクラウド化する。一方、RGBの画像列は、最初に
ステレオビジョンアルゴリズムで処理され、3D点が生成されます。
プリミティブ検出のためのクラウドレディ。

## RGB動画 (Visual-SLAM)


## RGB-D画像


## パターン照射＋画像認識
![](https://robotics.hiroshima-u.ac.jp/wp/assets/images/hyper_human_applications/blink_dot/concept-1.png)
少し前（2015年くらい）のロボットとかで、ロボットの頭上のカメラから地面にひし形の模様をレーザー照射しているロボットがたまにありますが、あれは照射したパターンが地面にどのように写っているかをカメラで捉えることで地面の形状を推定しています。

現在はRealsenseの一部の機種などがこの技術を採用していますが、パターンやそのマッチング方法が企業秘密になったりしていて技術がOpenになっているイメージは少ないですが、、、

（少し違いますが）ミクロな分野では、材料にランダムな模様をつけてカメラでその様子を捉えながら引張試験とかをすることで、その材料の変形の仕方などを探ることができます。


## 4. (2と似てるけど) エピポーラ幾何
![](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F581710%2F92400031-c68f-342e-efad-b4247825c50b.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&w=1400&fit=max&s=ef7acaf29da3a4212fe39108255c41ac)

にゃ！！

- https://qiita.com/ssdsad/items/f5857c7774794a6e5f5e


# 参考文献
- [A Survey of Simple Geometric Primitives Detection Methods for Captured 3D Data](https://perso.telecom-paristech.fr/boubek/papers/GeoPrimFitSurvey/GeoPrimFitSurvey.pdf)

