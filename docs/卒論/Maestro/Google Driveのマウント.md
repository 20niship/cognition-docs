
概要
------------------

google-drive-ocamlfuse([https://github.com/astrada/google-drive-ocamlfuse)](https://github.com/astrada/google-drive-ocamlfuse))を利用して、Google Drive をローカルにマウントする方法をまとめます。  
研究室のメールアドレスに紐付いているGoogle Driveは容量が無限にある(はず)なのでNASなどと上手く併用できると良いと思います。



インストール
----------------------

上記のgithubの通りに行う。
```sh
sudo add-apt-repository ppa:alessandro-strada/ppa
sudo apt update
sudo apt install google-drive-ocamlfus
```


基本的な使い方
-----------------------

初回利用時に、
```
google-drive-ocamlfus
```
とすると、ブラウザが立ち上がるので適切なアカウントでログインしてマウントを許可する。  
サーバ上ではブラウザがインストールされていないことがあるのでその際は、現在のディレクトリにfirefoxという名前で
```sh
#! /bin/sh
echo $\* > /dev/stderr
```
のようなファイルを作り、権限を変更して以下のように実行する(ライブラリを騙す)。
```sh
chmod 777 firefox
PATH=\`pwd\`:$PATH google-drive-ocamlfuse
```
認証用のURLが表示されるのでローカルのブラウザで認証する。  

認証できたら、マウントポイントを作りマウントする。
```sh
mkdir googledrive
google-drive-ocamlfus googledrive/
```
アンマウントする際は、
```sh
fusermount -u googledrive
```


Google Drive の特定のファイルをマウントしたい時
------------------------------

以下のようにすればできる。
```
cp -f ~/.gdfuse/default/config config
sed -i -e "s/^root_folder=$/root_folder=GOOGLE_DRIVE_FOLDER_ID/" config 

google-drive-ocamlfuse -config ./config -cc googledrive
```
GOOGLE_DRIVE_FOLDER_IDは、Google Driveでフォルダを開いた時の[https://drive.google.com/drive/folders/XXXXXXXXX](https://drive.google.com/drive/folders/XXXXXXXXX) の XXXXXXXXX の部分の事らしい。  
流れとしては、新しい設定ファイルを作り、それを基にマウントしているだけ。



参考
------------------

- [https://github.com/astrada/google-drive-ocamlfuse](https://github.com/astrada/google-drive-ocamlfuse)  
- [http://moguno.hatenablog.jp/entry/2016/03/24/010502](http://moguno.hatenablog.jp/entry/2016/03/24/010502)  
- [https://qiita.com/kikuchi_kentaro/items/65be0cf40ac61849d841#%E7%89%B9%E5%AE%9A%E3%83%95%E3%82%A9%E3%83%AB%E3%83%80%E4%BB%A5%E4%B8%8B%E3%81%AE%E3%81%BF%E3%83%9E%E3%82%A6%E3%83%B3%E3%83%88](https://qiita.com/kikuchi_kentaro/items/65be0cf40ac61849d841#%E7%89%B9%E5%AE%9A%E3%83%95%E3%82%A9%E3%83%AB%E3%83%80%E4%BB%A5%E4%B8%8B%E3%81%AE%E3%81%BF%E3%83%9E%E3%82%A6%E3%83%B3%E3%83%88)