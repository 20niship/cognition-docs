## インストール

### Cygwinで使うときは

参考にしたURL： [http://www.dh.aist.go.jp/~kimura/opencv/opencv-1.0.html.ja](http://www.dh.aist.go.jp/~kimura/opencv/opencv-1.0.html.ja)

上記URLからパッチをダウンロードして  
```sh
$ patch --binary -p1 < /tmp/opencv-1.0.cygwin-patch
$ make -f make_all_cygwin.mak
$ make -f make_all_cygwin.mak install PREFIX=/some/where/install/path/
```
PREFIXは  `/usr/local/` にしました。  

参考にしたURL2： [http://webcs.sit.ac.jp/wiki/index.php?OpenCV](http://webcs.sit.ac.jp/wiki/index.php?OpenCV)

上記URLからcv_cygwin.shをダウンロード、実行  
```sh
./cv_cygwin.h
```
コンパイル文は  
```sh
gcc -I/usr/local/include/opencv test test.c -lcxcore -lcv -lhighgui -lcvaux -lml
```
で通りました。  
（上記URL内のコンパイル文と微妙に違うので注意。test.c の前の test を追加）
