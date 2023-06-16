電源ON/OFF
-------------

*   胸のボタン長押し



ソフトウェアの導入
--------------

*   python
    *   以下からライブラリをダウンロード(要登録) [https://community.aldebaran.com/en/resources/software](https://community.aldebaran.com/en/resources/software)
*   python Naoqi SDKから適当に選んでダウンロードして解凍
*   中にライブラリが入っているので、環境変数を設定してどこからでも読み込めるようにする
```sh
  tar -zxvf pynaoqi-python2.7-xxx-linux64.tar.gz
  cat > ~/.bashrc
  export PYTHONPATH=(your download directory)/pynaoqi-python2.7-xxx-linux64/:$PYTHONPATH
  (Ctl+D)
  source ~/.bashrc
```
*   動作確認
  - Naoを有線で研究室のネットワークにつないで、胸のボタンを押すと自分のIPアドレスを喋る(192.168.xxx.xxx)
  - 以下のスクリプトをpythonインタプリタで実行
```sh
  from naoqi import ALProxy
  tts = ALProxy("ALTextToSpeech", "192.168.xxx.xxx", 9559)
  tts.say("hello world")
```
  - hello worldと喋れば成功
*   pythonのサンプルコード
    *   [http://doc.aldebaran.com/1-14/dev/python/examples.html](http://doc.aldebaran.com/1-14/dev/python/examples.html)
    *   [https://robotics.naist.jp/edu/text/?Robotics%2FNAO%2FSDK-Python](https://robotics.naist.jp/edu/text/?Robotics%2FNAO%2FSDK-Python)
    *   [http://numericalbrain.org/wp-content/uploads/kinematics201309202332.pdf](http://numericalbrain.org/wp-content/uploads/kinematics201309202332.pdf)
*   APIの一覧
    *   [http://doc.aldebaran.com/2-4/naoqi/index.html](http://doc.aldebaran.com/2-4/naoqi/index.html)

複数動作(動きながらデータとるとか)するときはマルチスレッドするとできます



無線で使いたい場合
--------------

*   NAOの設定
    *   NAOを一旦有線でつなぎ、ブラウザでNAOのIPアドレスにアクセス (デフォルトはuser:nao pass:nao)
    *   ログインできたら、ネットワーク設定から無線LANを設定して完了
    *   カメラを使う場合は有線でないと速度が足りないそうです



NAOの起動
-----------

*   NAOのIPアドレスを確認
    *   NAOを1クリック -> 聞き取る、ブラウザでアクセス
        *   user ID:nao
        *   passwd:nao
    *   カメラ使う人は有線で，無線はやはり遅い
*   動作後
    *   NAOを2クリック -> サーボモータのstiffnessを0に（プログラム終了条件に追加しておくほうがよい）



NAO プログラム
--------------

*   11/9から11/16までの更新内容（20111116.tar.gz）
    *   動作が終わるとstiffnessが0になります
*   pythonからboost-pythonというものを使って、c++を扱えるようになりました。
*   python側の変更点 カメラ画像取得と動作生成が一緒にできませんでしたが、スレッドを使い並行処理できるようになりました。  
    カメラの画像取得はNaoCamスレッド、動作生成はRobotクラスが担当しています。  
    NaoCamの中run内では、c++で作られたimg_moduleにカメラ画像を送って処理してもらってます。
*   c++側（img_module.cpp） Imgクラスのmy_imgproc関数が、NaoCamから画像を受け取って画素毎に処理してます。  
    色重心とかをpythonがわに返したいときはreturn_coc_x関数とか使ってみてください。  
    新しく関数作るときは、プログラム最下BOOST_PYTHON_MODULEの中に登録しておくと、pythonから呼び出せるようになります。
*   c++を変更したら、「sh mak.sh」でコンパイルしてください。
