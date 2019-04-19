# solにおけるPython3の導入

ここでは，電気通信大学の学内教育系サーバであるsolへのPython3の導入方法について解説します．
solについて詳しくは[電気通信大学情報基盤センターのホームページ](https://www.cc.uec.ac.jp/)を参照してください．

solにはPython3が入っていないため，使いたい場合には自分で導入する必要があります．
solそのものに管理者権限を用いてPython3等をインストールすれば，何も困ることは無いかもしれません．
しかしながら，我々にそんな権限が与えられることはありません．
そうなると，管理者権限を用いず自分のホームディレクトリ以下で何とかして必要な諸々をインストールすることになります．
今回は，**Homebrew**をホームディレクトリ以下で導入し，Homebrewを用いてPython3のインストールを行う方法を解説します．
なお，本内容は[筆者のブログ内容](https://nersonu.hatenablog.com/entry/2018/12/17/000000)を本ドキュメント用に再度まとめたものとなります．

## Homebrewの導入

mac OSには[**Homebrew**](https://brew.sh/index_ja)と呼ばれるパッケージマネージャがあります．
このHomebrewにLinux版として[**Linuxbrew**](http://linuxbrew.sh/)がありましたが，現在はHomebrewにまとめられています。
Homebrewを用いることで，Python3を始めとした様々なパッケージを手軽に管理者権限無しでインストールすることができます．
ここではまず，solにHomebrewを導入する手順を述べます．
solのシェルはデフォルトではtcshであるため，tcshで利用する前提で解説します．
bashなどに変えている方は適時等価なコマンド等で置き換えてください．

### proxyの設定をする

後々`git`コマンドを用いて外部ネットワークからHomebrewを持ってくるために，proxyサーバの設定を行っておきます．

```sh
$ setenv http_proxy http://proxy.uec.ac.jp:8080
$ setenv https_proxy http://proxy.uec.ac.jp:8080
$ setenv ftp_proxy http://proxy.uec.ac.jp:8080
$ setenv HTTP_PROXY http://proxy.uec.ac.jp:8080
$ setenv HTTPS_PROXY http://proxy.uec.ac.jp:8080
$ setenv FTP_PROXY http://proxy.uec.ac.jp:8080
```

今後何かと必要になる設定ですので，もし設定してない方は`~/.tcshrc`等に書いておくと，solログイン時に反映されるので便利だと思います．

### Homebrewを入れる

`git clone`してHomebrewのgithubのリポジトリから，Homebrewをとってくるだけで大丈夫です．
場所は任意の場所で結構です．
ここでは`~/local/`にします．

```sh
$ cd ~/local/
$ git clone https://github.com/Homebrew/brew.git
```

`git clone`出来たら，`brew`コマンドが使えるように`PATH`を通します．
また，brewのcacheの設定等も行っておきます．
`~/.tcshrc`に以下を書きます．

```sh
setenv HOMEBREW_CACHE $HOME/local/brew/cache
setenv PATH $HOME/local/brew/bin:${PATH}
setenv PATH $HOME/local/brew/sbin:${PATH}
```

設定を反映させ`brew doctor`で以下の出力を得られれば，Homebrewの導入は完了です．

```sh
$ source ~/.tcshrc
$ brew doctor
Your system is ready to brew
```

影響のないWarningは見逃しても構いません．

## Python3の導入

Homebrewを用いてPython3をインストールします．
基本的には`brew install {インストールしたいパッケージ名}`とするだけです．

今回はのちにプロットした画像を表示させるための依存関係を解決するために，Python3のインストール前にtcl-tkというパッケージをインストールします．
**必ず**Python3のインストール前に行ってください．

```sh
$ brew update
$ brew install tcl-tk
$ brew install python3
```

うまくインストールが完了すると，🍺が出力されます．また，正しくインストールできていれば，恐らく次のようなバージョンとなっていることでしょう．

```sh
$ python3 -V
Python 3.7.x
$ pip3 -V
pip xx.x.x from ... (python 3.7)
```
