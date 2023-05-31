# 保存



# AviUtlの場合

# ini File Parser

設定ファイルをiniファイルで読み込み/書き込むとき、iniパーサーが必要になる。

## iniファイルに書き込むもの

+ Window Style 
+ Window Color (dark, light, classic,  HighContrastDark)
+ Workspace
+ 最近使用したフォルダ
+ 最近使用した色
+ デフォルトの設定
+ バージョン情報

## ini sample text

iniファイルは以下のようなもの

```cpp
[workspace]


[style]
type=sample      
data_dir=C:   // 画像などの相対ディレクトリ
width=1920     // 画面サイズ
height=1080       

[color:dark]
Col_WidgetBg=255,255,255
Col_WidgetBg2=100,255,10
.......
```

追加設定↓

+ カンマで値を区切ることができる。
+ [header]の中を「-」で区切ることで階層化
+ //以下の行はコメント
+ 文字は「""」でくくる（くくったら、その中に//や改行が入っていても無視する）


### AviUtlのexoの文字コードについて

+ 一文字につき4桁ずつでUnicodeを使用（16進数）
+ 4桁のうち上二けたと下二桁は入れ替わっている。
+ 改行については不明

```cpp
a=6100
b=6200
c=6300
A=4100
E=4500
z=7a00
+=2b00
!=21000000 63000d000a003000310032003300000000
 （半角スペース）=20000000 63000d000a003000310032003300000000
0=3000
1=3100
2=3200
3=3300
abcn123=6100 6200 63000 d000a00 3000 3100 3200 3300 00000000000000
あ=42300000 63000d000a0030003100320033000000
あ=4230
    
```

exoファイル書き出し？のPythonスクリプト
Reference -> https://pandanote.info/?p=2422

```python
#!/usr/bin/env python3

# -*- coding: utf-8 -*-

import io
import sys
import re
import codecs
from struct import *
from binascii import hexlify


# 標準出力の文字コードを変更する。
sys.stdout = io.TextIOWrapper(sys.stdout.buffer,encoding='cp932')


if len(sys.argv) <= 2:
    print("Usage: txt2exo.py <template file> <text file>")
    sys.exit(1)

templatefile = sys.argv[1]
textfile = sys.argv[2]
exedit = {}
zero = {}
# 現在のセクションの文字列をそのまま入れます。
currentsection = "exedit"
originalstart = 0
originalend = 0
sectionsnum = 0

with open(templatefile,mode='r',encoding='shift_jis') as f:
    for line in f:
        m = re.match(r'\[([^\]]+)',line)
        if m:
            if m.group(1) != "exedit":
                currentsection = m.group(1)
                mm = re.match(r'^\d+$',m.group(1))
                if mm:
                    sectionsnum = int(currentsection)

        elif re.match(r'[^\r\n\s]',line):
            kv = line.split('=',1)
            if currentsection == "exedit":
                exedit[kv[0]] = kv[1]
            else:
                if currentsection not in zero:
                    zero[currentsection] = {}
                zero[currentsection][kv[0]] = kv[1]
                if kv[0] == 'start':
                    if (currentsection == 0):
                        originalstart = int(kv[1])
                elif kv[0] == 'end':
                    originalend = int(kv[1])

texts = [line.rstrip('\n').rstrip('\r') for line in open(textfile,"r",encoding="UTF-8") if re.match(r'^[^\r\n]',line)]

print("[exedit]")
for k,v in exedit.items():
    print("{0:s}={1:s}".format(k,v),end="")


sectionsnum = sectionsnum + 1
sectioncount = 0
deltaframes = originalend-originalstart
startpos = originalstart
currentsection = 0


for text in texts:
    for k,v in zero.items():
        m = re.match(r'^\d+$',k)
        if m:
            currentsection = sectioncount+int(k)
            print("[{0:d}]".format(currentsection))
            for vk,vv in v.items():
                if vk == "start":
                    print("{0:s}={1:d}".format(vk,startpos+int(vv)))
                elif vk == "end":
                    print("{0:s}={1:d}".format(vk,startpos+int(vv)))
                else:
                    print("{0:s}={1:s}".format(vk,vv),end="")
        else:
            sectionname = re.sub(r'^\d+',str(currentsection),k)
            print("[{0:s}]".format(sectionname))

            for vk,vv in v.items():
                if vk == "text":
                    text_in_utf16 = hexlify(text.encode('utf-16'))
                    hex_str = text_in_utf16.decode("ascii")[4:]
                    print("text={0:s}".format(hex_str),end="")
                    for x in range(len(hex_str),4096,4):
                        print("0000",end="")
                    print("")
                else:
                    print("{0:s}={1:s}".format(vk,vv),end="")
    sectioncount = sectioncount + sectionsnum
    startpos = startpos + deltaframes
```


