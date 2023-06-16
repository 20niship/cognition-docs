#### Qtでキャプチャ:

Qtで表示している画像を保存する方法の例
```cpp
	QImage pic( buffer, width, height, 32, 0, 0, QImage::IgnoreEndian );
	pic.save( QString::number( num ) + ".bmp", "BMP" );
```
などとして、動画の場合は連番のBMPファイルを作り、  
convertコマンドなどで動画にしましょう。  
Premireでもできるようです。  
但しbufferはRGBA計32ビットずつ並んだのuchar配列で、 ファイル名も00001-99999のように桁をあわせとくべきです。  
(上のソースは桁があわないのでsprintfとか使ってください)  
ウィジェットに直書きしてる場合は上でなく、
```cpp
	QPixmap::grabWidget( mywidget ).save( QString::number( num ) + ".bmp", "BMP" );
```
でウィジェットの内容ごと書き出せます。  
ちなみにgrabWidgetではXウィジェットに変換してる場合やGLを使ってる場合、  
書き出されたBMPが真っ白または真っ黒になって使えないと思います。

できたBMPをimagemagickでmpegにする場合、先に  
[ftp://ftp.mpegtv.com/pub/mpeg/mssg/mpeg2vidcodec_v12.tar.gz  
](ftp://ftp.mpegtv.com/pub/mpeg/mssg/mpeg2vidcodec_v12.tar.gz&br;)を落としてコンパイルし、./mpeg2/src/mpeg2enc/mpeg2encode を/usr/local/binにでもコピーしときましょう。

	`convert \*.bmp movie.mpg`

で作れます。フレームレートの調整は

	`convert -delay 3.3 \*.bmp movie.mpg`

のようにすれば可能です。  

*   delayオプションの後の数字はフレーム間の秒数を1/100秒単位で示したものです。



#### Qtでキャプチャ(QGL):

QGLなら
```cpp
	QPixmap pm = myglwidget.renderPixmap();
```
で直接Pixmapにレンダリングできます。 bmpへの保存は上と同様に。 ただ別クラスでGLlistにした内容が反映されない…。なぜ？


#### 追記

やってみたけど結構遅いです。複雑なシーンだと相当きついかも。 あとThreadプログラムだと文句いわれて落ちちゃいます。 非対応のようです。


#### Qtでキャプチャ2(QGL):

ThreadプログラムでもQGLWidgetを継承するクラスの中のpaintGL()関数内 であれば画像を保存することができます。

saiで例えるなら、GLCameraクラスのpiantGL()です。 以下ようなかんじでできます。
```cpp
	int w = width();
	int h = height();
	QImage image  = QImage( w, h, 32 );
	glReadPixels( 0, 0, w, h, GL_RGBA, GL_UNSIGNED_BYTE, image.bits() );
	image = image.swapRGB().mirror();
	image.save((QString)numstr + ".bmp", "BMP");
```
