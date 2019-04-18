# リモート接続でJupyter Notebookを使う

[Jupyter Notebook](https://jupyter.org/)とは，インタラクティブ(対話形式)にPythonのコードを実行でき，その実行結果をノート形式で記録できるツールです．
デフォルトのデータ出力や，グラフ出力も良い感じにしてくれるので，データ解析において用いられることがよくあります．
Jupyter NotebookはWebアプリケーションであり，ブラウザ上で実行します．
しかし，sol上でJupyter Notebookを起動しても，そのまま手元のマシンで使うことは困難です．
ここでは，SSHポートフォワーディングを用いた方法でこれを解決します．

## Jupyter Notebookのインストール

`pip`を用いてJupyter Notebookに必要なPythonパッケージを導入します．

```sh
$ pip3 install jupyter
```

依存関係が多いので少しインストールに時間がかかるかもしれませんが，これで終了です．

ここで，手元のマシンから`ssh`を用いてリモート接続していることを前提として，Jupyter Notebookを起動してみます．
起動は作業したいディレクトリ上で次のようにします．

```sh
$ jupyter notebook
```

恐らくエラーが出たり，指定された(例えば)`http://localhost:8888/...`に手元のマシンでアクセスしても何も起こらないかと思います．
それもそのはずで，リモート接続先の`localhost:8888`にJupyter Notebookが立っているのに，手元のマシンでは何も起こっていません．

## SSHポートフォワーディングによる方法

X転送でリモート接続先のブラウザを表示させる方法もありますが，ここではSSHポートフォワーディングを用いた方法を解説します．
仕組みについては詳しく説明しませんので，興味のある方は自分で調べてみてください．

まず，Jupyter Notebook側で下準備を行います．

### パスワードの生成

まず，Jupyter Notebookにアクセスする際のパスワードを生成します．
以下のようにして，IPythonを起動します．

```sh
$ ipython3
```

IPythonは標準の対話型インタプリタを強力にしたもので，Jupyterをインストールしたときの依存パッケージとして，既に使えるようになっているはずです．
IPython上でパスワードを次のようにして生成します．

```python
In [1]: from notebook.auth import passwd

In [2]: passwd()

Enter password: {任意のパスワード}
Verify password: {同じパスワードを入力}
Out[2]: 'sha1:{SHA-1でハッシュ化されたパスワード}'
```

ここでハッシュ化されたパスワードが次で必要になりますので，どこかに記録しておいてください．
用が済んだら，IPythonを終了させます．

```python
In [3]: quit()
```

### configファイルの設定

まずは，Jupyterのconfigファイルを生成します．

```sh
$ jupyter notebook --generate-config
Writing default config to: {自分のホームディレクトリ}/.jupyter/jupyter_notebook_config.py
```

基本的には`~/.jupyter/`に`jupyter_notebook_config.py`が生成されるはずです．
このPythonファイルを編集します．
好きなエディタで`jupyter_notebook_config.py`を開いてください．
以下の例では`nano`を使っています．

```sh
$ nano ~/.jupyter/jupyter_notebook_config.py 
```

次に該当部分のコメントアウト"`#`"を外し，以下のように編集して保存します．

```python
## The IP address the notebook server will listen on.
c.NotebookApp.ip = '0.0.0.0'

## Whether to open in a browser after starting. The specific browser used is
#  platform dependent and determined by the python standard library `webbrowser`
#  module, unless it is overridden using the --browser (NotebookApp.browser)
#  configuration option.
c.NotebookApp.open_browser = False

## Hashed password to use for web authentication.
#
#  To generate, type in a python/IPython shell:
#
#    from notebook.auth import passwd; passwd()
#
#  The string should be of the form type:salt:hashed-password.
c.NotebookApp.password = 'sha1:{先程生成したハッシュ化されたパスワード}'

## The port the notebook server will listen on.
c.NotebookApp.port = 8888
```

これらの変更は次の意味を持ちます．

- `c.NotebookApp.ip`: アクセスするIPアドレスの指定で，ここでは全て受け入れるようにした．
- `c.NotebookApp.open_browser`: デフォルトでは起動時にブラウザが立ち上がるので，オフにした．
- `c.NotebookApp.password`: Jupyter起動時の認証で用いるパスワードを設定した．
- `c.NotebookApp.port`: Jupyter Notebookに割り当てるポート番号を設定した．ポートフォワーディングに必要で，sol上で誰か先に使っていると使えないので柔軟に設定してください．

## 実際にリモート接続でJupyter Notebookを使ってみる

SSHする際に，SSHポートフォワーディングを用います．
例えば，以下のようにして手元のマシンからsolにSSH接続しましょう．

```sh
$ ssh -L 8888:localhost:8888 {ユーザ名}@sol.edu.cc.uec.ac.jp
```

リモート接続先でJupyter Notebookを起動します．

```sh
$ jupyter notebook
[I **:**:**.*** NotebookApp] Serving notebooks from local directory: {起動したディレクトリのパス}
[I **:**:**.*** NotebookApp] The Jupyter Notebook is running at:
[I **:**:**.*** NotebookApp] http://(sol.cc.uec.ac.jp or 127.0.0.1):8888/
[I **:**:**.*** NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
```

sol上のポート8888番でJupyter Notebookが立ち上がっています．
SSH接続時に`localhost:8888`をポート8888番にローカルフォワードしているので，手元のマシンのブラウザから`localhost:8888`にアクセスしてください．
Jupyterのパスワード認証画面が出ていれば成功です．
`passwd()`で設定したパスワードを入力することで，Jupyterを使用することができます．

### 補足

もし，次のようにした場合はどうでしょうか．

```sh
$ ssh -L 8889:localhost:8888 {ユーザ名}@sol.edu.cc.uec.ac.jp
```
この場合は手元のマシンの8889番にローカルフォワードしているので，手元のマシンのブラウザから`localhost:8889`にアクセスすればよいです．

すなわち，

```sh
$ ssh -L {手元マシンのポート番号}:{solでJupyterが立っているところ}:{solでJupyterが立っているところのポート番号} {ユーザ名}@sol.edu.cc.uec.ac.jp
```

というイメージを持ってもらえばいいかと思います．
詳細は解説しませんので，気になる方は各自調べてみてください．
