# 必要なPythonパッケージの導入

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

## Numpy と Scipy

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

## Matplotlib と Seaborn

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

## pandas

```sh
$ pip3 install pandas
```

[**pandas**](https://pandas.pydata.org/)は，データ分析に特化したデータ構造とツールを提供するパッケージです．
*DataFrame*と呼ばれるデータ構造を用いて，表やデータに対し簡単に演算処理や，結合・分離等の処理を行うことができます．

- 参考:
  - [Getting started](http://pandas.pydata.org/pandas-docs/stable/getting_started/index.html)

## scikit-learn

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

<!---
  statsmodels を入れるか検討
  https://www.statsmodels.org/stable/index.html

  時系列分析・回帰分析等に利用可能
-->
