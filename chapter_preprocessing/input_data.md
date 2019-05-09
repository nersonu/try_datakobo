# データの入力と概要を掴む

ここでは，主にpandasを用いたデータの取扱いについて解説します．
まずはデータの入力，簡単な統計量の算出方法からやってみましょう．

ここでは，例として次のデータファイルを用います．
[*Iris Data Set*](https://archive.ics.uci.edu/ml/datasets/Iris)と呼ばれる最も有名なデータセットの一つで，[UCI Machine Learning Repository](https://archive.ics.uci.edu/ml/index.php)から拝借します．
以下のコマンドを実行して，任意のディレクトリ下に保存しておいてください．

```sh
$ wget https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data -O iris.csv
```

実はscikit-leanを使ってこのデータを呼び出すことが出来るのですが，今回は練習のために，Webサイトから持ってきます．

## ファイルの読み込み

Pythonでpandasを使ってcsvファイルを読み込んでみましょう．
ここでは`read_csv()`というメソッドを用います．

```sh
$ python3
```

```python
>>> import pandas as pd
>>> iris_data = pd.read_csv("iris.csv")
>>> iris_data
     5.1  3.5  1.4  0.2     Iris-setosa
0    4.9  3.0  1.4  0.2     Iris-setosa
1    4.7  3.2  1.3  0.2     Iris-setosa
2    4.6  3.1  1.5  0.2     Iris-setosa
3    5.0  3.6  1.4  0.2     Iris-setosa
...
145  6.3  2.5  5.0  1.9  Iris-virginica
146  6.5  3.0  5.2  2.0  Iris-virginica
147  6.2  3.4  5.4  2.3  Iris-virginica
148  5.9  3.0  5.1  1.8  Iris-virginica

[149 rows x 5 columns]
```

整った形で読み込めているのがわかるでしょうか．

```python
>>> type(iris_data)
<class 'pandas.core.frame.DataFrame'>
```

このように`read_csv()`で読み込んだデータは，データ分析に特化したデータ構造である`DataFrame`形式で保存されます．
この`DataFrame`は様々な処理を容易にする関数が用意されていたり，可視化や機械学習手法に適用する際の相性も良いです．
このドキュメントでは積極的に`DataFrame`を扱っていくことにします．

ところで，`DataFrame`は行名と列名をデフォルトで持っています．
さて，先程読み込んだ`iris_data`ではどうなっているでしょうか．

```python
>>> iris_data
     5.1  3.5  1.4  0.2     Iris-setosa
0    4.9  3.0  1.4  0.2     Iris-setosa
1    4.7  3.2  1.3  0.2     Iris-setosa
2    4.6  3.1  1.5  0.2     Iris-setosa
3    5.0  3.6  1.4  0.2     Iris-setosa
...
145  6.3  2.5  5.0  1.9  Iris-virginica
146  6.5  3.0  5.2  2.0  Iris-virginica
147  6.2  3.4  5.4  2.3  Iris-virginica
148  5.9  3.0  5.1  1.8  Iris-virginica

[149 rows x 5 columns]
```

最も左にある列がここではそれぞれの行名を表しています．
何も指定しない標準の処理では，このように0,1,2...というようにindexの番号が上から割り振られていくようです．
では，列名はどうでしょうか．
先程は何も指定せずに読み込んだ結果，csvファイルの一番上の行がそのまま列名として処理されてしまっているようです．
これでは，うまくこの後の処理が出来ませんので，一手間加えましょう．
次のように，`read_csv()`を行う際に引数として`names`に列名のリスト(タプル等でも良い)を渡します．

```python
>>> iris_data = pd.read_csv("iris.csv", names=["sepal-length", "sepal-width", "petal-length", "petal-width", "class"])
>>> iris_data
     sepal-length  sepal-width  petal-length  petal-width           class
0             5.1          3.5           1.4          0.2     Iris-setosa
1             4.9          3.0           1.4          0.2     Iris-setosa
2             4.7          3.2           1.3          0.2     Iris-setosa
3             4.6          3.1           1.5          0.2     Iris-setosa
4             5.0          3.6           1.4          0.2     Iris-setosa
...
146           6.3          2.5           5.0          1.9  Iris-virginica
147           6.5          3.0           5.2          2.0  Iris-virginica
148           6.2          3.4           5.4          2.3  Iris-virginica
149           5.9          3.0           5.1          1.8  Iris-virginica

[150 rows x 5 columns]
```

今度はそれぞれの列に与えられた列名が入っており，先程列名になってしまっていた行も正しく読み込めています．
データによっては，headerと呼ばれる列名が記された行が1行目にあることがよくあります．
このような場合では`names`を指定せずに，そのままheaderの情報が列名としてうまく処理されるのです．

列名に限った話ではなく，データによっては行番号が記されているケースもあります．
データ読み込み後のスムーズな処理のために，データ読み込み時よく使う`read_csv()`の引数を紹介しておきます．

| 引数名 | よく用いられる型 | 説明 |
|:-------|:-----------------|:-----|
|sep|str|default: sep=','，区切り文字を変更|
|header|int|default: header=0，headerの行指定で無い場合header=Noneとする|
|names|list|列名指定|
|dtype|type|DataFrameに含まれるデータの型の指定|

詳しくは[pandas.read_csvのドキュメント](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html)を参照してください．

## データ概要を掴む

### データの表面的な特徴を見る

さて，読み込んだデータがどのようなものか，少し中身を覗いてみることにします．
まずはウォーミングアップとして，行数や列数を数えてみましょう．
リストの長さを測るように`len()`が使えます．

```python
>>> len(iris_data)
150
>>> len(iris_data.columns)
5
```

`columns`は列名が格納されたpandasのインデックス用のリストとなっています．

```python
>>> iris_data.columns
Index(['sepal-length', 'sepal-width', 'petal-length', 'petal-width', 'class'], dtype='object')
>>> type(iris_data.columns)
<class 'pandas.core.indexes.base.Index'>
```

また，`shape`はタプルで`(行数, 列数)`となっており，`size`は全要素数(すなわち，行数 * 列数)となっています．

```python
>>> iris_data.shape
(150, 5)
>>> iris_data.size
750
```

このようにすれば，データのサイズ感がなんとなくわかったかと思います．
閲覧するだけなら`info()`というメソッドを使うことで，さらにデータの表面的なものがわかってくることでしょう．

```python
>>> iris_data.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 150 entries, 0 to 149
Data columns (total 5 columns):
sepal-length    150 non-null float64
sepal-width     150 non-null float64
petal-length    150 non-null float64
petal-width     150 non-null float64
class           150 non-null object
dtypes: float64(4), object(1)
memory usage: 5.9+ KB
```

### データの種類

ここで，後の解説のためにデータの種類について触れておきます．

今回用いているIrisデータセットは，5つの列があり，それぞれの列で異なる素性を表しています．
それぞれの列が表すデータの意味は次の表の通りです．

|列名|意味|
|:---|:---|
|sepal-length|がく片の長さ|
|sepal-width|がく片の幅|
|petal-length|花びらの長さ|
|petal-width|花びらの幅|
|class|アヤメの品種名|

この中で*class*以外の4つは全て**数値データ**です．
かつ、連続な値をとる連続値のデータとなっています(ここでは詳しく解説しません)．
これに対し，*class*は品種の名前がそのまま入っていて，明らかに数値データではありません．
こういったデータのカテゴリやラベルを表すデータを**カテゴリデータ**といいます．
この二つは扱い方が少し変わってきます．
例えば，数値データには数値の大小や，平均値といったものをとることができますが，カテゴリデータではできません．
以下では，少しこの二つの違いに気を配りながら，データを眺めていきましょう．

### 簡単な統計量を算出する

まずは，それぞれの列が何個の要素を持っているか調べてみましょう．
`count()`メソッドで調べることが出来ます．

```python
>>> iris_data.count()
sepal-length    150
sepal-width     150
petal-length    150
petal-width     150
class           150
dtype: int64
```

それぞれデータの行数分の要素数があることがわかります．
データには**欠損**が含まれることもあり，欠損がある場合に`count()`で確認できる値が変化します．
欠損値の処理に関しては，後ほど詳しく解説します．

続いて，ユニークな値の要素の個数を見てみます．
すなわち，各列の値が何種類あるかといったことを確認するために用います．
こういう場合では，`nunique()`メソッドを用います．

```python
>>> iris_data.nunique()
sepal-length    35
sepal-width     23
petal-length    43
petal-width     22
class            3
dtype: int64
```

連続値を扱う数値データでは，これを見てもあまり情報が得られません．
しかしながら，カテゴリデータでは何種類のカテゴリがあるか，すなわち**カテゴリ数**を調べることができます．
すなわち，このデータには3種類のアヤメの品種が含まれていることを確認することができるわけです．

次は各列の最頻値を`mode()`メソッドを用いて確認します．

```python
>>> iris_data.mode()
   sepal-length  sepal-width  petal-length  petal-width            class
0           5.0          3.0           1.5          0.2      Iris-setosa
1           NaN          NaN           NaN          NaN  Iris-versicolor
2           NaN          NaN           NaN          NaN   Iris-virginica
```

最頻値が複数ある場合，上のように複数行にわたって表示されます．
すなわちここまでの結果で，3種類のアヤメの品種のデータ数はそれぞれ等しいということがわかります．
この事実を実際の値を見て確認してみましょう．
まず，*class*列を取り出しましょう．
`DataFrame`のある一つの列だけデータを取り出したいとき，次のようにします．

```python
>>> iris_data["class"]
0         Iris-setosa
1         Iris-setosa
2         Iris-setosa
3         Iris-setosa
4         Iris-setosa
...
146    Iris-virginica
147    Iris-virginica
148    Iris-virginica
149    Iris-virginica
Name: class, Length: 150, dtype: object
```

もしくは，次のようにします．

```python
>>> iris_data.loc[:, "class"]
0         Iris-setosa
1         Iris-setosa
2         Iris-setosa
3         Iris-setosa
4         Iris-setosa
...
146    Iris-virginica
147    Iris-virginica
148    Iris-virginica
149    Iris-virginica
Name: class, Length: 150, dtype: object
```

```python
>>> type(iris_data["class"])
<class 'pandas.core.series.Series'>
>>> type(iris_data.loc[:, "class"])
<class 'pandas.core.series.Series'>
```

これらは，`DataFrame`ではなく`Series`オブジェクトといい，`DataFrame`の1列バージョンだと考えてください．
このような1列のデータに特化したメソッドが用意されているのが特徴です．
我々もその恩恵を受けていきましょう．
それぞれのユニークな要素の数を見るには，`value_counts()`を用いて次のようにします．

```python
>>> iris_data.loc[:, "class"].value_counts()
Iris-setosa        50
Iris-versicolor    50
Iris-virginica     50
Name: class, dtype: int64
```

このようにしても，アヤメの品種がそれぞれ50ずつ，すなわち同数であるということが確認できました．
`mode()`だけでは最頻値をとるデータの名前しかわかりませんでした．
実際，具体的な値が必要になることが多いので覚えておきましょう．

今度は，数値データに着目します．
各列の算術平均を得るには`mean()`メソッドを用います．

```python
>>> iris_data.mean(numeric_only=True)
sepal-length    5.843333
sepal-width     3.054000
petal-length    3.758667
petal-width     1.198667
dtype: float64
```

`numeric_only=True`とすると，数値列のみ結果が得られます．
特に指定しない場合では，その場合に応じて適当な結果を返してくれます．

また，(不偏)標準偏差を得るには`std()`メソッドを用いて次のようにします．

```python
>>> iris_data.std(numeric_only=True)
sepal-length    0.828066
sepal-width     0.433594
petal-length    1.764420
petal-width     0.763161
dtype: float64
```

(不偏)分散を得るには`var()`メソッドです．

```python
>>> iris_data.var(numeric_only=True)
sepal-length    0.685694
sepal-width     0.188004
petal-length    3.113179
petal-width     0.582414
dtype: float64
```

平均や標準偏差・分散は，数値データの性質を知る上で，最も基本的な統計量です．
例えば，この中では*petal-length*が最もばらつきが多いデータということが見て取れます．

さらに，データのことをもっとよく理解するために少し応用してみましょう．
例えば，*class*が，すなわちアヤメの品種が*setosa*であるようなデータのみの平均を知りたいとき，まずは条件を満たすデータを取り出す必要があります．
例えば次のようにします．

```python
>>> iris_data[iris_data["class"] == "Iris-setosa"]
    sepal-length  sepal-width  ...  petal-width        class
0            5.1          3.5  ...          0.2  Iris-setosa
1            4.9          3.0  ...          0.2  Iris-setosa
2            4.7          3.2  ...          0.2  Iris-setosa
3            4.6          3.1  ...          0.2  Iris-setosa
4            5.0          3.6  ...          0.2  Iris-setosa
...
46           5.1          3.8  ...          0.2  Iris-setosa
47           4.6          3.2  ...          0.2  Iris-setosa
48           5.3          3.7  ...          0.2  Iris-setosa
49           5.0          3.3  ...          0.2  Iris-setosa

[50 rows x 5 columns]
```

`DataFrame`の`[]`の中に，元のデータ(ここでは`iris_data`)各行の値が`True, False`であるような`Series`を入れると，`True`となっている行だけを取り出した`DataFrame`を得ることができます．
実際，`iris_data["class"] == "Iris-setosa"`は，次のようになっています．

```python
>>> iris_data["class"] == "Iris-setosa"
0       True
1       True
2       True
3       True
4       True
...
146    False
147    False
148    False
149    False
Name: class, Length: 150, dtype: bool
```

あとはここで得られた結果に，`mean()`メソッドを用いるだけです．

```python
>>> iris_data[iris_data["class"] == "Iris-setosa"].mean()
sepal-length    5.006
sepal-width     3.418
petal-length    1.464
petal-width     0.244
dtype: float64
```

実際の解析では，品種といったようなカテゴリデータごとでの解析が必要になることがほとんどです．
こういった操作を行うことが今後もあるでしょう．

他にも，最大値を取得する`max()`メソッドや，最小値を取得する`min()`メソッド，中央値を取得する`median()`メソッドがあります．
これらはほぼ使い方が`mean()`等と同じなので，最後に四分位数の取得方法だけ解説していきます．
`quadtile()`メソッドを用いて，算出するパーセンタイルの値を引数としてリストで渡してやります．
具体的には次の通りです．

```python
>>> iris_data.quantile(q=[0.25, 0.75], numeric_only=True)
      sepal-length  sepal-width  petal-length  petal-width
0.25           5.1          2.8           1.6          0.3
0.75           6.4          3.3           5.1          1.8
```

ちなみに，`0.0`を指定すれば最小値，`0.5`を指定すれば中央値，`1.0`を指定すれば最大値です．

今回ここでは，具体的な統計量を確認していきました．
実は，このような値を簡単に確認するメソッドとして`describe()`があります．

```python
>>> iris_data.describe()
       sepal-length  sepal-width  petal-length  petal-width
count    150.000000   150.000000    150.000000   150.000000
mean       5.843333     3.054000      3.758667     1.198667
std        0.828066     0.433594      1.764420     0.763161
min        4.300000     2.000000      1.000000     0.100000
25%        5.100000     2.800000      1.600000     0.300000
50%        5.800000     3.000000      4.350000     1.300000
75%        6.400000     3.300000      5.100000     1.800000
max        7.900000     4.400000      6.900000     2.500000
```

データの概要を掴むためには，ともかくデータの中身の確認が必要不可欠です．
こういった簡単な統計量の算出も方法の一つとなり得るでしょう．
