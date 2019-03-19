# 事前準備

## solにおけるPython3の導入

ここでは，電気通信大学の学内教育系サーバであるsolへのPython3の導入方法について解説します．
solについて詳しくは[電気通信大学情報基盤センターのホームページ](https://www.cc.uec.ac.jp/)を参照してください．

solにはPython3が入っていないため，使いたい場合には自分で導入する必要があります．
solそのものに管理者権限を用いてPython3等をインストールすれば，何も困ることは無いかもしれません．
しかしながら，我々にそんな権限が与えられることはありません．
そうなると，管理者権限を用いず自分のホームディレクトリ以下で何とかして必要な諸々をインストールすることになります．
今回は，**Linuxbrew**をホームディレクトリ以下で導入し，Linuxbrewを用いてPython3のインストールを行う方法を解説します．
なお，本内容は[筆者のブログ内容](https://nersonu.hatenablog.com/entry/2018/12/17/000000)を本ドキュメント用に再度まとめたものとなります．

### Linuxbrewの導入

mac OSには[**Homebrew**](https://brew.sh/index_ja)と呼ばれるパッケージマネージャがあります．
このHomebrewのLinux版が[**Linuxbrew**](http://linuxbrew.sh/)というわけです．
Linuxbrewを用いることで，Python3を始めとした様々なパッケージを手軽に管理者権限無しでインストールすることができます．
ここではまず，solにLinuxbrewを導入する手順を述べます．
solのシェルはデフォルトではtcshであるため，tcshで利用する前提で解説します．
bashなどに変えている方は適時等価なコマンド等で置き換えてください．

#### proxyの設定をする

後々`git`コマンドを用いて外部ネットワークからLinuxbrewを持ってくるために，proxyサーバの設定を行っておきます．

```sh
$ setenv http_proxy http://proxy.uec.ac.jp:8080
$ setenv https_proxy http://proxy.uec.ac.jp:8080
$ setenv ftp_proxy http://proxy.uec.ac.jp:8080
$ setenv HTTP_PROXY http://proxy.uec.ac.jp:8080
$ setenv HTTPS_PROXY http://proxy.uec.ac.jp:8080
$ setenv FTP_PROXY http://proxy.uec.ac.jp:8080
```

今後何かと必要になる設定ですので，もし設定してない方は`~/.tcshrc`等に書いておくと，solログイン時に反映されるので便利だと思います．

#### Linuxbrewを入れる

`git clone`してLinuxbrewのgithubのリポジトリから，Linuxbrewをとってくるだけで大丈夫です．
場所は任意の場所で結構です．
ここでは`~/local/`にします．

```sh
$ cd ~/local/
$ git clone https://github.com/Linuxbrew/brew.git
```

`git clone`出来たら，`brew`コマンドが使えるように`PATH`を通します．
また，brewのcacheの設定等も行っておきます．
`~/.tcshrc`に以下を書きます．

```sh
setenv HOMEBREW_CACHE $HOME/local/brew/cache
setenv PATH $HOME/local/brew/bin:${PATH}
setenv PATH $HOME/local/brew/sbin:${PATH}
setenv LD_LIBRARY_PATH $HOME/local/brew/lib:${LD_LIBRARY_PATH}
```

これを書く前にコマンドラインで`printenv LD_LIBRARY_PATH`を実行してみてください．
もし何も出力がなければ上記の`LD_LIBRARY_PATH`の行の末尾にある`:${LD_LIBRARY_PATH}`は省略する必要があります．

設定を反映させ`brew doctor`で以下の出力を得られれば，Linuxbrewの導入は完了です．

```sh
$ source ~/.tcshrc
$ brew doctor
Your system is ready to brew
```

### Python3の導入

Linuxbrewを用いてPython3をインストールします．
`brew install {インストールしたいパッケージ名}`とするだけです．

```sh
$ brew update
$ brew install python3
```

うまくインストールが完了すると，🍺が出力されます．また，正しくインストールできていれば，恐らく次のようなバージョンとなっていることでしょう．

```sh
$ python3 -V
Python 3.7.x
$ pip3 -V
pip xx.x.x from ... (python 3.7)
```

## 必要なPythonパッケージの導入

ここでは，本ドキュメントで必要なPythonパッケージの導入を行います．
Pythonパッケージの導入は，基本的に`pip`と呼ばれるPythonのパッケージ管理ツールを用いて行うことにします．

なお，いま手元にあるPythonパッケージの一覧は，

```sh
$ pip3 list
```

もしくは

```sh
$ pip3 freeze
```

で確認することができます．

### Numpy と Scipy

```sh
$ pip3 install numpy
```

[**Numpy**](http://www.numpy.org/)は，Pythonで最もよく用いられるパッケージの一つです．
特に行列演算に強力な力を発揮する関数が多く含まれています．
演算精度の良さや，高速な処理が特徴の一つです．

また，さらに科学的な演算処理に特化した[**Scipy**](https://www.scipy.org/scipylib/index.html)と呼ばれるパッケージもあります．

```sh
$ pip3 install scipy
```

- 参考:
  - [Numpy and Scipy Documentation](https://docs.scipy.org/doc/)
  - [Quickstart tutorial](https://docs.scipy.org/doc/numpy/user/quickstart.html)

### Matplotlib と Seaborn

```sh
$ pip3 install matplotlib
```

[**Matplotlib**](https://matplotlib.org/)は，Pythonでよく用いられるプロット用のパッケージです．
数式のグラフ描画や，棒グラフやサンプルプロットといった統計的なグラフ描画，さらには画像描画も可能なパッケージです．

さらに，このMatplotlibを基に開発されている[**Seaborn**](https://seaborn.pydata.org/)というパッケージがあります．

```sh
$ pip3 install seaborn
```

Seabornを用いると，ヒストグラムやヒートマップが簡単に描画できます．
さらには，Matplotlibのグラフのテーマの拡張もあり，可視化処理の心強い味方です．

- 参考:
  - [Tutorials](https://matplotlib.org/tutorials/index.html)

### pandas

```sh
$ pip3 install pandas
```

[**pandas**](https://pandas.pydata.org/)は，データ分析に特化したデータ構造とツールを提供するパッケージです．
*DataFrame*と呼ばれるデータ構造を用いて，表やデータに対し簡単に演算処理や，結合・分離等の処理を行うことができます．

- 参考:
  - [Getting started](http://pandas.pydata.org/pandas-docs/stable/getting_started/index.html)

### scikit-learn

```sh
$ pip3 install scikit-learn
```

[**scikit-learn**](https://scikit-learn.org/)は，分類や回帰，クラスタリング等を機械学習手法を使って行うことができるパッケージです．
非常に簡単に扱うことができ，機械学習手法における前処理も行うことが出来ます．

また，不均衡なデータに対する前処理や機械学習手法を提供する[**imbalanced-learn**](https://imbalanced-learn.readthedocs.io/en/stable/)というパッケージもあります．

```sh
$ pip3 install imbalanced-learn
```

- 参考:
  - [scikit-learn Tutorials](https://scikit-learn.org/stable/tutorial/index.html)
