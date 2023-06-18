# Elmo driverについて諸々

概要
----------

*   高性能、多機能、小型軽量、と三拍子そろったモータードライバ
*   MAXON製含め、巷に出回るモータードライバと比べ圧倒的な性能を誇る
*   しかし圧倒的な高価格も誇る
*   様々なシリーズが存在するが、ISI的には小型軽量を極めたGoldTwitterが適すると思われる
*   参考
    *   [http://npm-ht.co.jp/suppliers/elmo.html](http://npm-ht.co.jp/suppliers/elmo.html)
    *   [http://www.elmomc.com/products/gold-digital-servo-drives-main.htm](http://www.elmomc.com/products/gold-digital-servo-drives-main.htm)


スペック
------------

*   公式ページを参照
*   Gold Solo Twitter G-SOLTWIR50/100SE1
    *   [https://www.elmomc.com/products/gold-solo-twitter-servo-drive.htm](https://www.elmomc.com/products/gold-solo-twitter-servo-drive.htm)
    - ![](img/maestro/g_soltwi_spec.png)
    *   10A以下のモデルはPhoenix Connector, 10A以上はWire connections
    *   PLC Sourceは12-30V。G-SOLTWIR50/100SE1はSTOがPLC Sourceなので、この電圧を印加できるシステムにする必要がある
    *   Analog InputはDifferential(±10V) or Single ended(0-3V)と書いてあるが、両方同時に入力可能
        *   分解能はともに12bit
    *   インクリメンタルエンコーダはラインドライバ入力(差動入力)なので、オープンコレクタ出力の場合、変換ICが必要
        *   例：AM26C31IN



### 購入歴のあるもの
*   G-TWI25/100SE \\94,000
*   G-SOLTWIR50/100SE1 \\98,000



使い方
-----------

*   マニュアル参照
    *   いずれ書きます



### EASⅡ


#### 概要
*   WindowsPCとモータドライバを接続して設定、ゲインチューニング、テスト、プログラミングなどが可能
*   会員登録さえすれば以下でDL可能
    *   [https://www.elmomc.com/products/application-studio/easii/download-easii/](https://www.elmomc.com/products/application-studio/easii/download-easii/)
*   接続方法はRS-232、CAN、USB、EtherCAT、UDP
    *   CANはCANモデルのみでKvaser社のCANインタフェースが必要
        *   RS-232と違って複数のモータドライバを一度に扱えて便利だが、結構高い。恐らくLeaf Light HS v2をChip 1 stopで買うのが最安(2018/11/11現在\\32,500)
        *   [https://www.chip1stop.com/product/detail?partId=KVAS-0000072&mpn=KVASER+LEAF+LIGHT+V2](https://www.chip1stop.com/product/detail?partId=KVAS-0000072&mpn=KVASER+LEAF+LIGHT+V2)
        *   初期設定ではBaudRate500、NodeID127になってる
    *   USB、EtherCAT、UDPはEtherCATモデルのみ
    *   RS-232はPCとGNDが接地されていないと壊れる場合があるので注意
*   DriveProgrammingは結構遅い。普通にPID制御を組むだけで2ms周期程度になる。



#### 起動
1. マエストロの認識
    1. デフォルトだと，drive01とかありますけど，【Remove】で消してください．maestroが自動で検出可能なドライバを見つけるので．
    1. workspace名のところを右クリック => 【Add Platinum Maestro EtherCAT】
    2.  右端【P01】を選択，【Target Connection】をOnlineにする．
    3. IPアドレスを聞かれるので，【IP Adress】から自分が設定したIPのものを選択
    4. 【Host IP Adress】を正しく選択
    5. 【Connect】
    6. 既に別の人が設定を作ってるなら，\[Upload configutration from maestro ...\]みたいなのを選択．
1. 注意事項
    * もし192.168.1.03とかだったらデフォルトのIPです．それ．変えてください



#### Tuningについて
*   Quick TuningとExpert Tuningが存在
    *   アクチュエータの特性を同定してドライバの不揮発目メモリに保存してくれる優れもの
    *   目標位置に動かす(指令：movePosition())だけや指令速度で動かす（指令：moveVelocity()）だけならすぐ使える
*   Encoder周りの設定に注意．Communicatio Timeだけはエンコーダの資料(Maxon ENX ABSシリーズ)からも読み取れず，デフォルトから変えて30という値にしたら動いた．
    *   同型ロボットハンドは【Expert Tuning】 => 【Drive Setup and Motion】 => 【Velocity and Position/Design】から見れるゲインを見て、直打ちして電流制御コードを書いた
    *   詳しくは[メカニカルグローブ](/)参照。そのままベタ書きしても動かなかったので。
*   Quick TuningのCurrentについて
    *   Continuous Stall Current \[A\]は，定格電流でok
    *   Peak Currentは定格電流の1.5倍~2.0倍程度
        *   間違っても突入電流にしない．モータが壊れる

*   Quick TuningはExpert Tuningで手動で設定できる値も決めてくれる
    *   ので，Expert Tuningじゃないと作れないパラメータや設定は基本ない



### MDS
*   Maestro Developer Studio．何故かNPMの人が持っているのに，ネットで使用法やダウンロード先が公開されてない．
*   なんか命令飛ばせないとか，バグ残ってるからでは？



#### 概要
*   WindowsPCとモータドライバを接続して設定、主にc++で多軸制御コードを開発したいときに使用．eclipseベースのIDE
*   旧GDSと言われるものかと．
*   ネットで手に入る方法が見当たらなかった．【is/samba/Thesis/kanai/App】以下に入手のための手がかりを置いておくので参照されたし
    *   それかこれを持ってる大村さんに聞く．



#### 起動
*   上部【Maestro】を選択 => create connection
*   【Discover】を選択，まともに設定で来てればmaestroが見つかる
*   画面右上の端 \[C/C++\]や\[Debug\]みたいなのの隣の【Remote System Explorer】を選択
*   左端の【Remote Systems】のタブ内に【Platinum-192.168.2.###】が見つかれば．接続成功．右クリックで【connect】を選択するとセッション開始
*   基本的に金井は
    *   【Ssh Terminal】を右クリック
    *   【Launch Terminal】
    *   から，cui上で色々動かしている



#### 開発
*   基本的にはeclipseと同じ使い勝手
*   以下の手順でsample codeを改造することがおすすめ．
    *   【File】 => 【New】 => 【C++ Project】 => 【Advanced Elmo Poject】
    *   「Advanced C++ Template」はそのままMocbus通信用のサーバーを立ち上げるコードになっている．
    *   【is/samba/Thesis/kanai/???】から金井が書いた遠隔操縦用コードも参照できるようにしておくので使ってください．
*   Maestroなら，PLATINUM_DやPLATINUM_Rでチェック項目が付いていればおｋ
*   実際に動作させるときは，画面上部の再生ボタンっぽいところから【Run configurations】で，connectionが正しく選択されているのを確認して【Run】
    *   基本的に，既にプログラムが走ってるMaestroにconnectionして【Run】すると，『既に走っているプログラムを優先するのでエラーが出る』
    *   ↑が厄介なのは，デバック中．
        *   プログラム中でpowerOn関数が呼ばれると，正常終了（terminal上でctrl + c）されない限りずっと「Run」状態になる
        *   つまり何も知らないと，プロジェクトが勝手に重複して勝手になにも実行できなくなる
        *   ↑は，[メカニカルグローブ](http://is/pukiwiki/index.php?%A5%E1%A5%AB%A5%CB%A5%AB%A5%EB%A5%B0%A5%ED%A1%BC%A5%D6 "メカニカルグローブ (1162d)")で述べた，「指令が重複している状態」
        *   なので，自分が開発した時は，この【Run】でエラーが出て，Terminal上ではちゃんと動かせるようにするために，引数を0みたいに一つ加えて，あえてセグフォでRunできなくした．
        *   なお，【Run】するための.pexeはここでマエストロにコピーされるので，後から↑のようにターミナル上で実行すればよい
    *   なお，既にMAestro上で走ってしまったコードを止める術はMDS上では確認されてない（clearとかやっても言うこと聞かなかった）
        *   よって，EASIIで【Restart】を書けるか，電源を引っこ抜くかをすぐにできるようにしておく．前者の方が早いからおすすめ．



### CAN通信
*   Elmoモータドライバとの通信はCANOpenと呼ばれるプロトコルに基づくCAN通信によって行われる。CANOpenはオブジェクトディクショナリと呼ばれるデータセットを各デバイスが持ち、それを通信によって読み書きすることで指令やセンサ読み取りを行うのが特徴。
*   以下がわかりやすい
    *   [http://www.softech.co.jp/mm_130904_plc.htm](http://www.softech.co.jp/mm_130904_plc.htm)

#### BeagleBoneBlackの準備
*   カーネルのバージョンが低いとできないかもしれない。以下はLinux beaglebone 4.4.68-ti-xenomai-r108で確認。
*   CANトランシーバが必要。MCP2551とか。
    *   [https://www.sparkfun.com/datasheets/DevTools/Arduino/MCP2551.pdf](https://www.sparkfun.com/datasheets/DevTools/Arduino/MCP2551.pdf)
*   device tree overlaysをインストール。以下の02を使うと楽。
    *   [https://github.com/brunoluiz/bbb-can-installer](https://github.com/brunoluiz/bbb-can-installer)
    *   以下で出てくるBB-CAN1がインストールされる。P9_24とP9_26がDCAN1として使われるようになる。
*   DCAN1を使用する場合、can_rxがP9_24、can_txがP9_26になってるので接続。一応GNDも接続。
    *  つなぎ方はSolo Twitterマニュアルの8.7.3.2とかに。
*   can-utilsも入れておく ` sudo apt-get install can-utils`     
*   CANの立ち上げ
```sh
sudo modprobe can
sudo modprobe can-dev
sudo modprobe can-raw
echo BB-CAN1 >/sys/devices/platform/bone_capemgr/slots
sudo ip link set can0 up type can bitrate 500000
sudo ifconfig can0 up
```
    
*   candumpを起動。CANのやりとりが見れる。
    * `candump can0`
*   Gold Twitter起動時にメッセージが受信されるはず
    * `can0  77F   \[1\]  00`
    *   先頭の3桁は識別子IDで、小さいほど優先順位が高い。CANOpenではCOB-IDと呼ばれ、大抵はCANデータのタイプ+ノード番号で表され、メッセージの種類と送信元が識別可能。今回は0x700がError controlと呼ばれるタイプで、0x7F(127)がノード番号。
    *   \[\]内はデータのバイト数、その後が実際のCANデータ



#### CANOpenSocket
*   Linuxで使えるフリーのCANOpenライブラリ。コマンドライン上からSDO (Service Data Object)通信とNMT(Network Management Object)通信ができるので、ちょっと試したいときに便利。
*   かなり作り込まれていはいるが、ちょっと遅い？
    *   [https://github.com/CANopenNode/CANopenSocket](https://github.com/CANopenNode/CANopenSocket)
*   マスタノード立ち上げ
```sh
root@beaglebone:~/CANopenSocket/canopend# app/canopend can0 -i 3 -c ""
app/canopend - starting CANopen device with Node ID 3(0x03), count=1 ...
app/canopend - communication reset ...
app/canopend - Command interface on socket '/tmp/CO_command_socket' started ...
app/canopend - running .
```
    
*   canopencommを使ってSDO通信によってオブジェクトディクショナリを読み書きする。オブジェクトディクショナリの仕様はマニュアルをDS-301,DS-402あたりで検索
*   例えば以下のように0x1017に1000を書き込むと、1秒間隔でハートビートが送られてくるのがcandumpで確認できる。


```sh
root@beaglebone:~/CANopenSocket/canopencomm# ./canopencomm \[1\] 127 write 0x1017 0 i16 1000
\[1\] OK
root@beaglebone:~/CANopenSocket/canopencomm# candump can0
    can0  77F   \[1\]  7F
    can0  77F   \[1\]  7F
    can0  77F   \[1\]  7F
    can0  77F   \[1\]  7F

*   Position actual valueはアドレスが0x6064、Data typeがINTEGER32なので、

root@beaglebone:~/CANopenSocket/canopencomm# ./canopencomm \[1\] 127 read 0x6064 0 i32
\[1\] 88946
```
    
*   あとはcanopencomm/main.cを参考に自分のプログラムに組み込むこともできる。
*   デフォルトではかなり遅いが、canopend/src/CO_master.cを見るとnanosleepという関数で5msとか10msスリープしているので、コメントアウトするとだいぶ早くなる。
    *   ただしこれでもSDO通信で1MbpsでPosition actual valueを1度読むのに0.8msほどかかる
    *   /canopend/src/CO_command.cのsdoClientUploadで0.65msくらい、write(fd, resp, respLen)で0.15msくらいかかっているっぽい。
    *   CAN通信自体は1Mbpsなら理論上0.1msもかからないはずだが。



#### Epos2
*   [https://github.com/sperre/epos2](https://github.com/sperre/epos2)
    *   本来はMAXONモータを駆動するものだが、かなりシンプルに書かれているので少し書き換えると汎用的に使える
    *   通信はおよそ0.5ms程度



#### PDO(Process Data Object) 通信
*   SDO通信は一度の要求で一つの情報しかやりとりできず効率が悪いので、センサ値・指令といった速報性の高い情報はPDO通信を使う
*   特にTPDO(Transmit PDO)と RPDO(Receive PDO)はマスタからのSYNC信号と同期して情報がやりとりできるためリアルタイム性の高い通信が可能なはず
    *   TPDOはマスタがSYNC信号を送ったときに全デバイスから送られる
    *   RPDOはマスタが送っても即座には更新されず、SYNCを送ったときに始めてオブジェクトが更新される
    *   設定(Transmittion type)によってはSYNC通信n回おきとかも可能
*   TPDOの簡単な例 (EASIIのマニュアルにあるExample)
    *   0x1800の31bit目を1にして、TPDO1をnot validに
        *   31bit目が0のときは他のビットを変更できないので、変更内容は中身を一旦確認してから適宜
```sh            
root@beaglebone: ~/CANOpenSocket/canopencomm# ./canopencomm \[1\] 127 w 0x1800 1 u32 0xC00001FF
#  TPDO1のマッピングをクリア(0x1A00のsubindex1 = Number of mapped application objects in PDO)


root@beaglebone: ~/CANopenSocket/canopencomm# ./canopencomm \[1\] 127 w 0x1A00 0 u8 0x00
# 16bit長(0x10)の0x6041 (Status word) subindex 0 (0x00)をTPDO1の sub index1(0x01)に登録
    
root@beaglebone: ~/CANopenSocket/canopencomm# ./canopencomm \[1\] 127 w 0x1A00 1 u32 0x60410010
    
#  32bit長(0x20)の0x6064 (Position actual value) subindex0 (0x00)をTPDO1の sub index2(0x02)に登録
    
root@beaglebone:/mnt/usb/CANopenSocket/canopencomm# ./canopencomm \[1\] 127 w 0x1A00 2 u32 0x60640020
# 2つのオブジェクトをTPDO1にマップ
    
root@beaglebone: ~/CANopenSocket/canopencomm# ./canopencomm \[1\] 127 w 0x1A00 0 u8 0x02   
# TPDO1のTransmission TypeをSyncのたびにTPDOを送る(0x01)に変更
    
root@beaglebone: ~/CANopenSocket/canopencomm# ./canopencomm \[1\] 127 w 0x1800 2 u8 0x01  
# TPDO1をvalidに
    
root@beaglebone: ~/CANOpenSocket/canopencomm# ./canopencomm \[1\] 127 w 0x1800 1 u32 0x400001FF
#  一旦stopしてから再スタート
    
root@beaglebone: ~/CANopenSocket/canopencomm# ./canopencomm 127 stop
    \[1\] OK
root@beaglebone: ~/CANopenSocket/canopencomm# ./canopencomm 127 start
    \[1\] OK
    
# syncオブジェクト(COB-IDが0x80、データなし)を送る
    
root@beaglebone: ~/CANopenSocket/canopencomm# cansend can0 080#
    
# candumpを見るとTPDOが来てる
    can0  080   \[0\]
    can0  1FF   \[6\]  C0 02 1B 08 00 00
    
# これは0x6041が0x02C0 0x6064が0x0000081Bという意味
# TPDO1のCOB-IDは0x180+ノード番号なので、0x180+0x7F=0x1FF
```       



マスターモーションコントローラ（P-MAS0N/マエストロ）
--------------------------------------



### 概要
*   [トングシステム](http://is/pukiwiki/index.php?%A5%C8%A5%F3%A5%B0%A5%B7%A5%B9%A5%C6%A5%E0 "トングシステム (1163d)")と[メカニカルグローブ](http://is/pukiwiki/index.php?%A5%E1%A5%AB%A5%CB%A5%AB%A5%EB%A5%B0%A5%ED%A1%BC%A5%D6 "メカニカルグローブ (1162d)")で利用．文字通り，各ドライバのマスターデバイスとなり，一括制御ができる．
*   以下では[メカニカルグローブ](http://is/pukiwiki/index.php?%A5%E1%A5%AB%A5%CB%A5%AB%A5%EB%A5%B0%A5%ED%A1%BC%A5%D6 "メカニカルグローブ (1162d)")開発で実際に利用した（P-MANSON x Modbus通信（EtherNetの一種）x MDSによるC++開発 )について紹介（なお，[トングシステム](http://is/pukiwiki/index.php?%A5%C8%A5%F3%A5%B0%A5%B7%A5%B9%A5%C6%A5%E0 "トングシステム (1163d)")はMDSではなく，EASIIによるPLC開発を行っている）．
*   全体の説明：[https://www.elmomc.com/product/easii/](https://www.elmomc.com/product/easii/)



### 大雑把な開発の流れ
配線 -> マエストロの設定変更 -> EASIIによるドライブの認識と設定の変更（例：Torqueの送受信を有効にする） -> EASIIによる各ドライブのチューニング -> EASIIかMDSによるソフトウェア開発 -> HostPC側でmodbus送受信ができる制御用コードを開発



### 準備


#### マエストロ周り
*   電源供給：
*   IPアドレス変更：
    *   ほかのPCとIPが重複の内容にしましょう．
    *   USB接続のみでしか変更できないので注意．
*   配線
    *   特に難しい箇所はなし
*   EAS上での設定
    *   正しく配線しておけば，以下を実行することで勝手にIPとかも割り振ってくれる（連番になる？これは不可避みたい．）
        -  Workspace -> \[P01(指定したmaestroのID)\]を右クリック -> \[Add NewPlatinum Maestro\] -> 画面中央付近の\[Target Connection/Connection Type\]をofflineから変更
*   電流制御する場合には，MaestroからElmoに電流命令を飛ばせるようにする必要がある（デフォルトではオフ）ので，その設定．
    -  Workspace -> \[P01(指定したmaestroのID)\]を右クリック -> \[Edit EtherCAT configuration\] -> axisを選択(a01(Twitter)とか) -> FMMU/SM -> SM2(output)から\[0X160C Target Torque\]を追加
    - ![](img/maestro/add_torque_control_option.PNG)


#### IPアドレス（とサブマスク）の変更
*   参考1: [https://www.elmomc.com/capabilities/motion-control/host-communication/getting-started-with-g-mas-connections/](https://www.elmomc.com/capabilities/motion-control/host-communication/getting-started-with-g-mas-connections/)
*   参考2: [https://www.elmomc.com/members/NetHelp1/Elmo.htm#!gmasinitialconnectio.htm](https://www.elmomc.com/members/NetHelp1/Elmo.htm#!gmasinitialconnectio.htm)
*   手順
    *   これからUSB接続してマエストロとつなげようとしているホストのPCが，COMポートを見れるか確認．
        *   リスト内に，ポート（COMとLPT）が表示されているかを見る．
        *   無きゃ表示されるPCを借りて来た方がいい．ASUSのノートはダメだった．
    *   maestroのDeviceUSBとホストPCを接続
    *   Tera Termでシリアル通信から\[ELMO GMAS\]を選択して接続，IPアドレス変更のキーを入れましょう．Wikiを見て重複の無いIPにセット．2019年現在nidoking
    *   なお，マエストロで制御するelmoは，指定したipに対して連番になる．よって，十分に指定ipの後ろに空きがあるものにしよう．
    *   サブマスクをisi仕様にする．255.255.0.0
    *   rebootする（しないと認識されない！！）
*   Tera Termで使用するであろうコマンドたち
    *   現在のipアドレスの確認（引数無し） or 変更（引数に変更後のipを入れる）
```sh        
ipaddr
ipaddr 192.168.######
```
    *   サブマスクの確認（引数無し） or 変更（引数に変更後のサブマスクを入れる）
```sh
ipmask 255.255.0.0
```



#### ドライバ周り
*   STOに通電させることを忘れず．logicから分岐させて構わない．



#### Host PC周り
*   modbusライブラリのインストール
    *   参照：[http://linux-memory.blogspot.com/2012/12/libmodbus.html](http://linux-memory.blogspot.com/2012/12/libmodbus.html)
    *   ソースのダウンロード：[http://libmodbus.org/](http://libmodbus.org/)
    *   解凍後，解凍されたディレクトリ内で以下を実行
```sh
> ./configure
> make
> make install
```
*   なお，上記だけだとうまくいかず（cmake, makeともに進むが，libmodbus.so.5がNotFoundになった．確認は以下のコマンドでしましょう），仕方なく/lib/x86_64-linux-gnu/にlibmodbus.so.5をsudo cpしたらうまく行った。 : ` ldd <実行ファイル名>`
*   libmodbus.so.5はおそらく，最初は/usr/local/libに保存されてる



### ソフトウェア開発


#### Maestro Developer Studio（MDS,昔はGDSと呼ばれる）による開発
*   開発言語：C/C++
*   なお，旧名称がG-MAS Developer Studioであり，未だにElmoの公式サイトでさえGDSの説明のみしかないので注意．



#### EASIIによる開発
*   開発言語：IEC61131-3
*   Cライクな工業規格言語での制御．トングシステムはこれで動く．



Tips
------------



### Tuning時にエンコーダのカウントが思った通りに変化しない
*   エンコーダのbit設定に問題あるかも．



### Quick Tuning時>Motor setting >Maximum currentはモータの突入電流ではない
*   タイトルの通り．定格電流の整数倍（2倍とか3倍とか）にしてください．平気で指定した電流を流されます．
*   突入電流なんて指定したものなら，モータ壊されます



### telnetでログイン可能
*   cui上で操作したいなら，telnetでログイン可能．usename, passwordともにuserで入れる
    `$ telnet 192.168.2.### `
*   例はこんなかんじ．
  - ![](img/maestro/telnet_example.JPG)


### マエストロでの開発
*   マエストロは一度バックグラウンドでプログラムを実行すると，そのプログラムが正常終了されない限り，他のプログラムを実行させない仕組みになっている（UDP Error　ID:-16あたりが出る）
*   よって，電源を落とすかEASIIでResetするなりして，所望のプログラムの実行・デバックをしましょう．



### アースの効能？（まだ検証の域）
*   24V電源供給で\[G-SOLTWI 10/100EE7\]を使用しているとき，Power Onと同時にモータがぶん回る事態が発生
*   ドライバのPEをちゃんとどっか導体に接地させたらおさまった



### PWMの周波数（電流サンプリングタイム）とチョークコイル
*   参照：[https://www.maxongroup.co.jp/medias/sys_master/8815464153118.pdf?attachment=true](https://www.maxongroup.co.jp/medias/sys_master/8815464153118.pdf?attachment=true)
*   症状：いかに記述するような電流制御不安定化，Quick Tuning時のGain error
*   一回り小さい\[G-SOLTWI 10/100EE7\]場合，デフォルトは100usとなっている．
*  注意しすべき点として，アクチュエータとの相性が悪いと電流リップルが異常に大きくなり，制御が不安的になる．不安定の例は以下
    *   Quick Tuning時に異常発熱．余裕で発煙する．普通に触れない．
    *   指令電流を全く追従しない．
    *   Expert Tuning -> Current verification -> step応答・周波数応答ともに完全に発振
    *   なお，発振はIゲインを完全に0にすればおさまる．定常偏差はひどいことになりますが．
*   特に低インダクタンスを売りにしているMAXONモータかつ小型かつ24V以下などの低電圧で動かす場合は深刻．グローブは全部条件満たしちゃった．．．
*   よって，各相ごとにチョークコイルを入れ，合計インダクタンスを少なくとも0.2mH以上にする（NPM篠崎様談）．またMaxonモータなら，推奨インダクタンス値を公表してるため，その式をもとにチョークコイルを選定する．
*   なお，チョークコイルの導入により抑えるリップル電流は電流サンプリングタイムの値にも依存．したがって，これをデフォルトから下げるのも手．
    *   EASII -> 画面上の（Floating Tools ）をクリック -> Terminal起動 -> \`TS=50\`と打つことで，サンプリングタイム変更．
    *   なお，ただ\`TS\`と打つと，現在指定している電流サンプリングタイムが表示される．デフォルトに戻すなら\`TS=100\`



### Rシリーズについて
*   型式に"R"とついてるものがあるが、これは大電流モデルで、連続電流とピーク電流が同じ
*   ついていないものは連続電流の倍がピーク電流
*   型番には連続電流が記されている。25と50Rは何が違うのか聞いてみたが、特に変わらないらしい。



### 電流分解能
*   ピーク電流を1.25倍したものを、11ビット分割したもの（1ビットは符号)。



### STO(Safety torque off)
*   このピンに電圧供給しないと電流が流れない
*   トングシステムでは，logic用の電源から，STOとlogic端子に分岐させて電圧供給して普通に動いている．



### Analog Input (G-SOLTWIR50/100SE1)
*   センサのGNDをCOMRETと接続しないと激しいノイズが入る。Supply Voltageの-と同じGNDを使っていてもダメだった。
*   Differentialの接続図によると24番ピンがANALOG RETとなっている。表ではCOMRETになっている。



#### EASIIで，ちょくちょくイーサネット接続が切れる
*   lanコネクタ(5Pin側)の接触不良が怪しいです
*   ケーブルキットを購入できるなら，付属でついてきた【LAN端子-5 pin端子】のケーブルを積極利用しましょう．LAN端子側を切り落とせば，【5 pin - 5 pin端子】のイーサキャットコネクタケーブルが簡単に作れる


## 接続

### 1\. EASIIから制御モードの変更
*   EAS II 立ち上げ => 【Maestro Setup and Motion】（画面左下） => 各axisの【OP Mode】を『Cyclic Torque』へ変更
*   『Cyclic Torque』が表示されてない場合
    *   モータドライバの方でmode changeのシグナルを受け取れない設定になっている可能性が高い．
    * このページの「準備」の章を見る


### 2\. Server 立ち上げ


#### MDSからやる場合
*   maestroとconnct : 【Remote】（画面右上）=> 【Platinum-192.168.2.###】を右クリック => 【connect】
*   ターミナル起動：【Remote】（画面右上）=> 【Platinum-192.168.2.###/Ssh Terminals】（###は対応するIP）を右クリック => 【Launch Terminal】
    *   Remoteの位置だけど，最小化や終了タブの真下，となりに【Debug】とかある
*   terminal上で以下，
    
    ./remoteSecondGlove.pexe 0
    


#### メモ
*   引数0はデバックで楽だったころの名残．入れてね．役割ないけど．
*   うまくいった時　：　mastroの体内時計が10ms感覚で吐かれ続ける
*   失敗したとき　：　なんか終了する．
*   以下が出た場合（失敗時の一例）
```sh
-----------------------------------------
----     Get Error/Warning           ----
----     From API function           ----
-----------------------------------------
Function name:  MMC_OpenUdpChannelCmd:
Return value :  -1
ErrorID      :  -16
```
    *   マエストロ上でドライバに命令を送ったのに，それを終了させずに重複して命令を送ってますよ的なエラー
    *   これは現状以下の解決策で強制的にスクリプトを終わらせるしかない．
        *   マエストロのコンセントをぶちぬく
        *   【EAS II】　=> 【System Configuration】（画面左下） => 【Restart】（画面上部）
    *   【MDS】=>なにやってもダメだった．「clear project」とかやっても応答がない．やっぱバグってるだろMDS
    *   これが出たらOP Modeもリセットされるので，また1からやり直しましょう．


### 3\. HostPCからスクリプト起動


#### その前に原点出し.csvを確認
*   ENC-USB-###_.csvが適切な場所に，適切に保存されているか確認．【remoteUR_and_GLB/enc_calib/build/】内のcsvがそう．
*   ない場合は原点出しを行う
    *   見方は，1行目がendcoderのid, 2行目が原点とする場所(rad)，3行目が回転方向および値域
    *   3行目は0が正（0 ~ 6piを使う場合），3行目は1が負（-6-9 ~ 0を使う場合），
    *   この3行目にあたる値域は，はグローブの取り付け方，あるいは原点出しのピンの位置によって使い分けている．注意
*   また，ENC-USB-###_.csvをあえて読まない設定をしている場合も存在．
    *   Glvlib/MtGlb_R.cpp内の関数【init_ElmoEnc()】内に，以下の処理がある場合は注意．
        * `Axisinterface_v2::reset_robotLoadEnc_and_robotMotorinptEnc(float v1, int v2);`
        *   Elmo　Driverが読み取った値は，通常maxon motorの入力軸に取り付けられたabsolute encoderの値
        *   これは，Elmo Driverの電源が落とされる度に（PCを再起動するたびに？）変わる．
        *   なので通常，トングのコードMtTgR.cppなど同様に，遠隔操縦のコードをhost PCから起動させた際には，まずelmoが獲得したモータ入力軸の値を読みに行き，これと出力軸エンコーダ（MAS3）の読み取った絶対値と対応させる．
        *   しかし，ガタや特異点のせいで，不感帯ができ，一番最初に読み取った値との対応が取れなくなることもある．
        *   特に，グローブ2号機の人差し指が顕著．メカ的に直さないといけないけど時間が無かった．
        *   よって，限界まで指を閉じた状態を原点にするよう，elmoの読み取ったエンコーダの値を手打ちして，そこをMAS-3の0.0radと対応させている
    *   この場合，対策方法は二つ
        *   1\. `reset_robotLoadEnc_and_robotMotorinptEnc(float v1, int v2);`をコメントアウトして，人差し指が閉じ切らないことを我慢して使う．
        *   2\. EASII => 【Maestro Setup and Motion】（画面左下） => 限界まで同型ハンドの人差し指の第二関節を閉じた時の，【a02】のActual position \[cnt\]をメモ．この値v2_を，その後，`reset_robotLoadEnc_and_robotMotorinptEnc(0.0, v2_)`とセットして，再度build


### 0\. Absolute Encoderの原点出し
*   【remoteUR_and_GLB/enc_calib/build/】内にcsvが無い，また明らかにずれている場合はこれが必要
*   まずmake
```sh
mkdir <path_to_remoteUR_and_GLB>/enc_calib/build/ && cd <path_to_remoteUR_and_GLB>/enc_calib/build/
cmake ..
make
```
    
*   次にコード．引数が少し複雑なので注意
    ` ./encCalib <Board Name> <Encoder ID> <Direction>`
    順を追って説明
    *   <Board Name> : Encoderの名前．大村さんが命名．例えば，二号機は'ENC-USB-GLB-R'とか
    *   <Encoder ID> : チャネル番号に対応，CNが一番若いものが0（一号機のMAS-3-INTERFACEはCN3, 二号機はCN1）,
    *   <Direction> : 指の可動域が原点からpos方向なのかnega方向なのかを指定．要は0度から突然6piとか-6piに飛ばないようにするためのもの．わからなかったら，とりあえず0とかにして，0点d出しのピンに関節を当ててから， 値が増えてるのか（この場合pos）減ってるのか(この場合nega)を見極める．
*   最後，原点に当てた状態でkey board入力すると，"./encCalib"のwhile loopを抜けて，その時の値と引数としてセットした<Direction>が値として保存される．
*   保存される.csvは，`./<Board Name> + str (_.csv)` となる


