# 基本的なコマンドラインツール

ここでは，データを扱うにあたっての基本的なコマンドラインツールを紹介します．
UNIXのコマンドを扱うことができれば，簡単なファイル操作はコマンドライン上で行うことが可能です．

特に何も変更をしていなければ，`echo $LANG`すると次のような出力が得られます．

```sh
$ echo $LANG
ja_JP.UTF-8
```

この状態を前提に解説を行います．
なお，`ja_JP.`の後の`UTF-8`はシステムの文字コードが*UTF-8*であることを示しています．

また，例として次のデータファイルを用います．
[*Car Evaluation*](https://archive.ics.uci.edu/ml/datasets/Car+Evaluation)と呼ばれるデータセットで，[UCI Machine Learning Repository](https://archive.ics.uci.edu/ml/index.php)から拝借します．
以下のコマンドを実行して，任意のディレクトリ下に保存しておいてください．

```sh
$ wget https://archive.ics.uci.edu/ml/machine-learning-databases/car/car.data -O car.csv
```

## ファイルの中身を眺める

最も単純な方法は`cat`コマンドを使う方法です．
さっそく*car.csv*の中身を覗いてみましょう．

```sh
$ cat car.csv
```

`cat`コマンドでは，ファイルの中身を一気に全て出力します．
行数が少ないファイルではそれで構わないのですが，もっと中身を上からじっくり眺めたい場合には不向きでしょう．
こういうときは，`less`を使います．

```sh
$ less car.csv
```

`less`コマンドは，ターミナルのウィンドウサイズ分のファイルの中身を先頭の行から表示します．
矢印上キーで上に送り，矢印下キーで下に送って，ファイル閲覧を快適なものにします．
`q`キーで閲覧の終了です．
行数が多いファイルでは特に重宝することでしょう．

また，先頭の行から何行閲覧したい，とはっきりしたものがあれば，`head`コマンドを使うと良いでしょう．
例えば，先頭から3行だけ見たい場合は次のようにします．

```sh
$ head -n 3 car.csv
vhigh,vhigh,2,2,small,low,unacc
vhigh,vhigh,2,2,small,med,unacc
vhigh,vhigh,2,2,small,high,unacc
```

逆に，末尾から見たい場合は`tail`を使うと良いでしょう．

```sh
$ tail -n 3 car.csv
low,low,5more,more,big,low,unacc
low,low,5more,more,big,med,good
low,low,5more,more,big,high,vgood
```

## ファイルの連結

`cat`コマンドは元々*catenate*(連結)を意味しています．
いま，次のような*ABC.txt*と*DEF.txt*という二つのファイルを用意しましょう．

```sh
$ cat ABC.txt
AAA
BBB
CCC
$ cat DEF.txt
DDD
EEE
FFF
```

この二つのファイルを`cat`を用いてファイルの連結をします．
`cat`コマンドの後に連結したいファイルを与えてあげましょう．

```sh
$ cat ABC.txt DEF.txt
AAA
BBB
CCC
DDD
EEE
FFF
```

パイプ`|`を使って`less`で連結後の中身も閲覧できます．

```sh
$ cat ABC.txt DEF.txt | less
```

連結した内容は出力として表れるだけなので，何かファイルに保存されるわけではありません．
連結した内容を別名で保存するにはリダイレクト`>`を使います．
連結後のファイル名を*ABCDEF.txt*とするならば次のようにします．

```sh
$ cat ABC.txt DEF.txt > ABCDEF.txt
```

## ファイルの分離

ファイルの分離には`split`コマンドを用います．
例えば*ABCDEF.txt*を2行ずつ分割するとき，`-l`オプションを用いて次のようにします．

```sh
$ split -l 2 filename_
$ cat filename_a
AAA
BBB
$ cat filename_b
CCC
DDD
$ cat filename_c
EEE
FFF
```

この場合では結果として，*filename\_* という名前をベースとした2行ずつの3つのファイルが出力されます．
デフォルトでは*a*,*b*,*c*...といった順番でベースとするファイル名の末尾に自動的に付与される形です．
`-d`オプションを使うと数字に変化します．
他にも，行以外の分割の基準を他のオプションで設定できますので，必要に応じて調べてみてください．

## 列方向のファイルの結合

「データを列方向で結合したい」というケースがよくあります．
行方向の結合は先に説明したように，`cat`を使えばよいのですが，列方向では`paste`コマンドを用います．

```sh
$ paste ABC.txt EDF.txt
AAA	DDD
BBB	EEE
CCC	FFF
```

デフォルトではタブ文字が間に挿入されますが，間の文字を指定することもできます．
例えば"`,`"で区切りたければ，`-d`オプションを用いて次のようにします．

```sh
$ paste -d , ABC.txt EDF.txt
AAA,DDD
BBB,EEE
CCC,FFF
```

元々が"`,`"区切りであるcsvファイルの列方向結合では上記の方法が便利です．

```sh
$ paste -d , car.csv car.csv | head -n 3
vhigh,vhigh,2,2,small,low,unacc,vhigh,vhigh,2,2,small,low,unacc
vhigh,vhigh,2,2,small,med,unacc,vhigh,vhigh,2,2,small,med,unacc
vhigh,vhigh,2,2,small,high,unacc,vhigh,vhigh,2,2,small,high,unacc
```

## 列単位でのファイルの内容抽出

「データの中で何列目だけ取り出したい」というケースがよくあります．
このような場合では`cut`コマンドを用います．
例えば，"`,`"を区切りとするcsvファイルの3列目を取り出したいというときには次のようにします．
(下記の例では，出力を短くするために`head`コマンドを用いています)

```sh
$ cut -d , -f 3 car.csv | head -n 3
2
2
2
```

`-d`オプションの後に区切り文字を指定し，`-f`オプションで列数を指定します．
もちろん，複数列の指定も可能です．
例えば，3列目から5列目とする場合は次の通りです．

```sh
$ cut -d , -f 3-5 car.csv | head -n 3
2,2,small
2,2,small
2,2,small
```

列が隣接していなくても構いません．
例えば1列目と3列目ならば次の通りです．

```sh
$ cut -d , -f 1,3 car.csv | head -n 3
vhigh,2
vhigh,2
vhigh,2
```

## ファイルの文字コードの確認と変換

今回の例で用いるファイルを持ってきます．

```sh
$ wget {url}
```

さて，この*shift\_jis\_sample.txt*は`cat`や`less`で中身を閲覧することができるでしょうか．

```sh
$ less shift_jis_sample.txt
```

一番上の英語のみで書かれた行以外は，文字化け等でうまく確認できないと思います．
実際のファイルの中身は以下のようになっています．

```
Shift-JIS
しふと
じす！
```

これは，最初に確認したシステムの文字コードが*UTF-8*であることに起因しています．
今回はファイル名でわかりますが，このファイルは文字コードが*Shift-JIS*となっており，システムの文字コードと異なるためうまく読めません．
ファイルの文字コードは，`nkf`コマンドを用いて次のように確認できます．

```sh
$ nkf --guess shift_jis_sample.txt
Shift_JIS (CRLF)
```

*Shift_JIS*がファイルの文字コードであり，その右の括弧内の*CRLF*はファイルの改行文字です．
中身を閲覧するためには，文字コードを*UTF-8*にしてあげます．
`nkf`コマンドを用いて次のようにします．

```sh
$ nkf -w shift_jis_sample.txt
Shift-JIS
しふと
じす！
```

`-w`オプションが*UTF-8*に変換するオプションです．
上書き保存する場合はさらに`--overwrite`オプションを付与します．
また，リダイレクトを用いれば別名保存もできます．

```sh
$ nkf -w shift_jis_sample.txt > no_shift_jis.txt
```
