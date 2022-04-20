.. _10min:

{{ header }}

********************
10 分 pandas
********************

これは pandas の初心者向けの短いイントロダクション。
:ref:`Cookbook<cookbook>` のより複雑なレシピを見ることが可能。

通常、以下のように import する。

.. ipython:: python

   import numpy as np
   import pandas as pd

オブジェクトの作成
---------------

:ref:`Intro to data structures section <dsintro>` を参照。

値のリストを渡すことによって :class:`Series` を生成し、pandas にデフォルトの整数インデックスを作成させる。

.. ipython:: python

   s = pd.Series([1, 3, 5, np.nan, 6, 8])
   s

ラベルの列、日付インデックスを含む Numpy 配列を渡すことによって :class:`DataFrame` を作成。

.. ipython:: python

   dates = pd.date_range("20130101", periods=6)
   dates
   df = pd.DataFrame(np.random.randn(6, 4), index=dates, columns=list("ABCD"))
   df

series ライクの構造体の中へ変換可能なオブジェクトの辞書 (連想配列) を渡すことによって :class:`DataFrame` を生成。

.. ipython:: python

   df2 = pd.DataFrame(
       {
           "A": 1.0,
           "B": pd.Timestamp("20130102"),
           "C": pd.Series(1, index=list(range(4)), dtype="float32"),
           "D": np.array([3] * 4, dtype="int32"),
           "E": pd.Categorical(["test", "train", "test", "train"]),
           "F": "foo",
       }
   )
   df2

得られた :class:`DataFrame` の列は異なる :ref:`dtypes <basics.dtypes>`: を持つ。

.. ipython:: python

   df2.dtypes

もし IPython を使用しているなら、列名のタブ補完 (及びパブリック属性) は自動的に有効にされる。
補完されるだろう属性は次の通り。

.. ipython::

   @verbatim
   In [1]: df2.<TAB>  # noqa: E225, E999
   df2.A                  df2.bool
   df2.abs                df2.boxplot
   df2.add                df2.C
   df2.add_prefix         df2.clip
   df2.add_suffix         df2.columns
   df2.align              df2.copy
   df2.all                df2.count
   df2.any                df2.combine
   df2.append             df2.D
   df2.apply              df2.describe
   df2.applymap           df2.diff
   df2.B                  df2.duplicated

このように、``A``、``B``、``C`` 及び ``D`` の列は自動的にタブ補完される。
``E`` と ``F`` もある。
残りの属性は簡潔にするために切り捨てられている。

データの表示
---------------

:ref:`Basics section <basics>` を参照。

ここにフレームの先頭と末尾を見る方法を示す。

.. ipython:: python

   df.head()
   df.tail(3)

インデックスと列の表示

.. ipython:: python

   df.index
   df.columns

:meth:`DataFrame.to_numpy` は基礎となるデータの Numpy 表現を与える。
:class:`DataFrame` が異なるデータ型の列を持つとき高度な操作が必要になるかもしれないことに注意する。
これは pandas と NumPy 間の基本的な違いのためである。
**Numpy の配列は配列全体に一つの dtype を持ち、一方 pandas の DataFrame は列ごとに一つの dtype を持つ**
:meth:`DataFrame.to_numpy` を呼ぶとき、pandas は DataFrame にある dtype の *すべて* に当てはまる NumPy の dtype を見つける。
これは最終的に ``object`` になる可能性があり、すべての値を Python オブジェクトにキャストする必要がある。

``df`` の場合、すべての浮動小数点数値の :class:`DataFrame` において、
:meth:`DataFrame.to_numpy` は高速でありデータのコピーを必要としない。

.. ipython:: python

   df.to_numpy()

``df2`` の場合、複数の dtype を持つ :class:`DataFrame` であり、
:meth:`DataFrame.to_numpy` は比較的高度。

.. ipython:: python

   df2.to_numpy()

.. note::

   :meth:`DataFrame.to_numpy` は出力にインデックスまたは列のラベルを含まない。

:func:`~DataFrame.describe` はデータの簡単な統計の概要を表示する。

.. ipython:: python

   df.describe()

データの転置:

.. ipython:: python

   df.T

軸によるソート:

.. ipython:: python

   df.sort_index(axis=1, ascending=False)

値によるソート:

.. ipython:: python

   df.sort_values(by="B")

選択
---------------

.. note::

   標準的な Python / Numpy 選択や設定は直観的でインタラクティブな作業に役立ち、
   プロダクションコードでは最適化された pandas データのアクセスメソッドである ``.at``、``.iat``、``.loc`` 及び ``.iloc`` を推奨する。

インデックス作成に関するドキュメントは :ref:`Indexing and Selecting Data <indexing>` および :ref:`MultiIndex / Advanced Indexing <advanced>` を参照。


取得
~~~~~~~

一つの列を選択すると、``df.A`` と同じ :class:`Series` を生成する。

.. ipython:: python

   df["A"]

行をスライスする ``[]`` で選択。

.. ipython:: python

   df[0:3]
   df["20130102":"20130104"]

ラベルによる選択
~~~~~~~~~~~~~~~~~~

詳しくは :ref:`Selection by Label <indexing.label>` を参照。

ラベルを使用し断面を取得する場合

.. ipython:: python

   df.loc[dates[0]]

ラベルによる複数軸での選択

.. ipython:: python

   df.loc[:, ["A", "B"]]

ラベルのスライスを表示すると、両端が *含まれる*

.. ipython:: python

   df.loc["20130102":"20130104", ["A", "B"]]

返されるオブジェクトの次元を小さくする。

.. ipython:: python

   df.loc["20130102", ["A", "B"]]

スカラー値を得る場合

.. ipython:: python

   df.loc[dates[0], "A"]

スカラーを高速に取得する場合 (前のメソッドと同様)

.. ipython:: python

   df.at[dates[0], "A"]

位置による選択
~~~~~~~~~~~~~~~~~~~~~

詳細は :ref:`Selection by Position <indexing.integer>` を参照

渡される整数の位置による選択

.. ipython:: python

   df.iloc[3]

NumPy/Python と同じようにスライスによる選択

.. ipython:: python

   df.iloc[3:5, 0:2]

NumPy/Python スタイルと同様に、位置のリストによる選択

.. ipython:: python

   df.iloc[[1, 2, 4], [0, 2]]

明示的に行をスライスする場合

.. ipython:: python

   df.iloc[1:3, :]

明示的に列をスライスする場合

.. ipython:: python

   df.iloc[:, 1:3]

明示的に値を取得する場合

.. ipython:: python

   df.iloc[1, 1]

スカラーに高速アクセスし取得する場合

.. ipython:: python

   df.iat[1, 1]

真偽値インデックス
~~~~~~~~~~~~~~~~

データを選択するため一つの列の値を使用

.. ipython:: python

   df[df["A"] > 0]

条件の合うデータフレームの値を選択

.. ipython:: python

   df[df > 0]

フィルタリングするために :func:`~Series.isin` メソッドを使用

.. ipython:: python

   df2 = df.copy()
   df2["E"] = ["one", "one", "two", "three", "four", "three"]
   df2
   df2[df2["E"].isin(["two", "four"])]

設定
~~~~~~~

新しい列の設定は自動的にインデックスによってデータを整列する。

.. ipython:: python

   s1 = pd.Series([1, 2, 3, 4, 5, 6], index=pd.date_range("20130102", periods=6))
   s1
   df["F"] = s1

ラベルによって値の設定

.. ipython:: python

   df.at[dates[0], "A"] = 0

位置によって値を設定

.. ipython:: python

   df.iat[0, 1] = 0

NumPy 配列の割り当てによって設定

.. ipython:: python

   df.loc[:, "D"] = np.array([5] * len(df))

事前の設定操作の結果

.. ipython:: python

   df

``where`` 操作により設定

.. ipython:: python

   df2 = df.copy()
   df2[df2 > 0] = -df2
   df2


欠損データ
------------

pandas は優先的に欠損データを代表するための値 ``np.nan`` を使用する。
これはデフォルトで計算に含まれない。
:ref:`Missing Data section<missing_data>` を参照

再インデックスは指定された軸において変更 / 追加 / 削除 ができる。
これはデータのコピーを返す。

.. ipython:: python

   df1 = df.reindex(index=dates[0:4], columns=list(df.columns) + ["E"])
   df1.loc[dates[0] : dates[1], "E"] = 1
   df1

欠損データを持ついくつかの行を削除するには

.. ipython:: python

   df1.dropna(how="any")

欠損データを埋めるには

.. ipython:: python

   df1.fillna(value=5)

値が ``nan`` である場所を真偽値でマスクして取得

.. ipython:: python

   pd.isna(df1)


操作
----------

:ref:`Basic section on Binary Ops <basics.binop>` を参照.

統計
~~~~~

一般的な操作では欠損データを除外する。

記述統計の実行

.. ipython:: python

   df.mean()

もう一方の軸でも同様に操作

.. ipython:: python

   df.mean(1)

異なる次元を持ち順序を必要とするオブジェクトの操作。
また、pandas は自動的に指定された次元に沿ってブロードキャストを行う。

.. ipython:: python

   s = pd.Series([1, 3, 5, np.nan, 6, 8], index=dates).shift(2)
   s
   df.sub(s, axis="index")


適用
~~~~~

データに関数を適用

.. ipython:: python

   df.apply(np.cumsum)
   df.apply(lambda x: x.max() - x.min())

ヒストグラムの作成
~~~~~~~~~~~~~

:ref:`Histogramming and Discretization <basics.discretization>` を参照。

.. ipython:: python

   s = pd.Series(np.random.randint(0, 7, size=10))
   s
   s.value_counts()

文字列メソッド
~~~~~~~~~~~~~~

Series は以下のコードスニペットのように ``str`` 属性において配列の各要素に対し簡単な文字列処理を行うメソッドが備わっている。
``str`` におけるパターンマッチングは一般的に通常 (場合によっては常に使用) では `regular expressions <https://docs.python.org/3/library/re.html>`__ を使用することに注意。
詳しくは :ref:`Vectorized String Methods<text.string_methods>` を参照。

.. ipython:: python

   s = pd.Series(["A", "B", "C", "Aaba", "Baca", np.nan, "CABA", "dog", "cat"])
   s.str.lower()

マージ
-----

結合
~~~~~~

pandas は、Series や DataFrame オブジェクトを簡単に組み合わせるための様々な機能を提供し、
インデックスのための様々な種類のセットロジックや、
join / merge タイプの操作の場合の関係代数的な機能を備える。

:ref:`Merging section <merging>` を参照。

:func:`concat`: で pandas オブジェクトを連結。

.. ipython:: python

   df = pd.DataFrame(np.random.randn(10, 4))
   df

   # break it into pieces
   pieces = [df[:3], df[3:7], df[7:]]

   pd.concat(pieces)

.. 備考::
   :class:`DataFrame` への列の追加は比較的速い。
   しかし、行の追加はコピーを必要とし高度である可能性がある。

   レコードを繰り返し追加することによる :class:`DataFrame` の作成の代わりに
   予め生成されたレコードのリストを :class:`DataFrame` コンストラクターに渡すことを推奨する。

Join
~~~~

SQL スタイルのマージ。:ref:`Database style joining <merging.join>` セクションを参照。

.. ipython:: python

   left = pd.DataFrame({"key": ["foo", "foo"], "lval": [1, 2]})
   right = pd.DataFrame({"key": ["foo", "foo"], "rval": [4, 5]})
   left
   right
   pd.merge(left, right, on="key")

もう一つ例を挙げると、

.. ipython:: python

   left = pd.DataFrame({"key": ["foo", "bar"], "lval": [1, 2]})
   right = pd.DataFrame({"key": ["foo", "bar"], "rval": [4, 5]})
   left
   right
   pd.merge(left, right, on="key")

グループ化
--------

"group by" によって次の一つ以上ステップに関与するプロセスを参照している。

 - **Splitting** はいくつかの基準に基づきデータをグループにまとめる
 - **Applying** は各グループに個別に機能
 - **Combining** は結果をデータ構造へ格納

:ref:`Grouping section <groupby>` を参照。

.. ipython:: python

   df = pd.DataFrame(
       {
           "A": ["foo", "bar", "foo", "bar", "foo", "bar", "foo", "foo"],
           "B": ["one", "one", "two", "three", "two", "two", "one", "three"],
           "C": np.random.randn(8),
           "D": np.random.randn(8),
       }
   )
   df

グループ化し、生成されたグループに対して :meth:`~pandas.core.groupby.GroupBy.sum` 関数を適用。

.. ipython:: python

   df.groupby("A").sum()

複数の列でグループ化すると、階層的なインデックスができ、この場合も :meth:`~pandas.core.groupby.GroupBy.sum` 関数を適用することが可能。

.. ipython:: python

   df.groupby(["A", "B"]).sum()

リシェイプ
---------

セクション :ref:`Hierarchical Indexing <advanced.hierarchical>` 及び :ref:`Reshaping <reshaping.stacking>` を参照。

スタック
~~~~~

.. ipython:: python

   tuples = list(
       zip(
           *[
               ["bar", "bar", "baz", "baz", "foo", "foo", "qux", "qux"],
               ["one", "two", "one", "two", "one", "two", "one", "two"],
           ]
       )
   )
   index = pd.MultiIndex.from_tuples(tuples, names=["first", "second"])
   df = pd.DataFrame(np.random.randn(8, 2), index=index, columns=["A", "B"])
   df2 = df[:4]
   df2

DataFrame の列において :meth:`~DataFrame.stack` メソッドは「圧縮」

.. ipython:: python

   stacked = df2.stack()
   stacked

「スタックされた」DataFrame または Series と同様に (``index`` として ``MultiIndex`` を持つ)、
逆の操作である :meth:`~DataFrame.stack` はデフォルトで **最後のレベル** をアンスタックする。

.. ipython:: python

   stacked.unstack()
   stacked.unstack(1)
   stacked.unstack(0)

ピボットテーブル
~~~~~~~~~~~~
:ref:`Pivot Tables <reshaping.pivot>` セクションを参照。

.. ipython:: python

   df = pd.DataFrame(
       {
           "A": ["one", "one", "two", "three"] * 3,
           "B": ["A", "B", "C"] * 4,
           "C": ["foo", "foo", "foo", "bar", "bar", "bar"] * 2,
           "D": np.random.randn(12),
           "E": np.random.randn(12),
       }
   )
   df

このデータから非常に簡単にピボットテーブルの作成をすることができる。

.. ipython:: python

   pd.pivot_table(df, values="D", index=["A", "B"], columns=["C"])


時系列
-----------

pandas はシンプルで、強力で周波数変換を行う上でリサンプリング操作を行うための効率的な機能性を持つ。
(例、秒単位のデータを 5 分単位に変換)
これは金融系のアプリケーションに限らず、非常に一般的である。
:ref:`Time Series section <timeseries>` を参照。

.. ipython:: python

   rng = pd.date_range("1/1/2012", periods=100, freq="S")
   ts = pd.Series(np.random.randint(0, 500, len(rng)), index=rng)
   ts.resample("5Min").sum()

タイムゾーン表現

.. ipython:: python

   rng = pd.date_range("3/6/2012 00:00", periods=5, freq="D")
   ts = pd.Series(np.random.randn(len(rng)), rng)
   ts
   ts_utc = ts.tz_localize("UTC")
   ts_utc

ほかのタイムゾーンへの変換

.. ipython:: python

   ts_utc.tz_convert("US/Eastern")

タイムスパン表現間の変換

.. ipython:: python

   rng = pd.date_range("1/1/2012", periods=5, freq="M")
   ts = pd.Series(np.random.randn(len(rng)), index=rng)
   ts
   ps = ts.to_period()
   ps
   ps.to_timestamp()

期間とタイムスタンプを変換することで、いくつかの便利な算術関数を使用することができる。
次の例では、11 月決算の四半期頻度を、四半期決算の翌月末の午前 9 時に変換しています。

.. ipython:: python

   prng = pd.period_range("1990Q1", "2000Q4", freq="Q-NOV")
   ts = pd.Series(np.random.randn(len(prng)), prng)
   ts.index = (prng.asfreq("M", "e") + 1).asfreq("H", "s") + 9
   ts.head()

カテゴリー別
------------

pandas は :class:`DataFrame` にカテゴリーデータを含めることが可能。
完全ドキュメントは、:ref:`categorical introduction <categorical>` 及び :ref:`API documentation <api.arrays.categorical>` を参照。

.. ipython:: python

    df = pd.DataFrame(
        {"id": [1, 2, 3, 4, 5, 6], "raw_grade": ["a", "b", "b", "a", "a", "e"]}
    )

生の成績をカテゴリー型に変換

.. ipython:: python

    df["grade"] = df["raw_grade"].astype("category")
    df["grade"]

カテゴリーの名前をより意味のある名前に変更 (:meth:`Series.cat.categories` への代入は実施済み！)。

.. ipython:: python

    df["grade"].cat.categories = ["very good", "good", "very bad"]

カテゴリーを並び変えると同時に、足りないカテゴリーを追加 (:meth:`Series.cat` 以下のメソッドはデフォルトで新しい :class:`Series` を返す)。

.. ipython:: python

    df["grade"] = df["grade"].cat.set_categories(
        ["very bad", "bad", "medium", "good", "very good"]
    )
    df["grade"]

並び替えは語彙順ではなく、カテゴリー順

.. ipython:: python

    df.sort_values(by="grade")

カテゴリー列でのグループ化は空のカテゴリーも表示する。

.. ipython:: python

    df.groupby("grade").size()

プロット
--------

:ref:`Plotting <visualization>` ドキュメントを参照。

matplotlib API を参照するための標準的な規則を使用

.. ipython:: python

   import matplotlib.pyplot as plt

   plt.close("all")

The :meth:`~plt.close` method is used to `close <https://matplotlib.org/3.1.1/api/_as_gen/matplotlib.pyplot.close.html>`__ a figure window:

.. ipython:: python

   ts = pd.Series(np.random.randn(1000), index=pd.date_range("1/1/2000", periods=1000))
   ts = ts.cumsum()

   @savefig series_plot_basic.png
   ts.plot();

If running under Jupyter Notebook, the plot will appear on :meth:`~ts.plot`.  Otherwise use
`matplotlib.pyplot.show <https://matplotlib.org/3.1.1/api/_as_gen/matplotlib.pyplot.show.html>`__ to show it or
`matplotlib.pyplot.savefig <https://matplotlib.org/3.1.1/api/_as_gen/matplotlib.pyplot.savefig.html>`__ to write it to a file.

.. ipython:: python

   plt.show();

On a DataFrame, the :meth:`~DataFrame.plot` method is a convenience to plot all
of the columns with labels:

.. ipython:: python

   df = pd.DataFrame(
       np.random.randn(1000, 4), index=ts.index, columns=["A", "B", "C", "D"]
   )

   df = df.cumsum()

   plt.figure();
   df.plot();
   @savefig frame_plot_basic.png
   plt.legend(loc='best');

Getting data in/out
-------------------

CSV
~~~

:ref:`Writing to a csv file: <io.store_in_csv>`

.. ipython:: python

   df.to_csv("foo.csv")

:ref:`Reading from a csv file: <io.read_csv_table>`

.. ipython:: python

   pd.read_csv("foo.csv")

.. ipython:: python
   :suppress:

   import os

   os.remove("foo.csv")

HDF5
~~~~

Reading and writing to :ref:`HDFStores <io.hdf5>`.

Writing to a HDF5 Store:

.. ipython:: python

   df.to_hdf("foo.h5", "df")

Reading from a HDF5 Store:

.. ipython:: python

   pd.read_hdf("foo.h5", "df")

.. ipython:: python
   :suppress:

   os.remove("foo.h5")

Excel
~~~~~

Reading and writing to :ref:`MS Excel <io.excel>`.

Writing to an excel file:

.. ipython:: python

   df.to_excel("foo.xlsx", sheet_name="Sheet1")

Reading from an excel file:

.. ipython:: python

   pd.read_excel("foo.xlsx", "Sheet1", index_col=None, na_values=["NA"])

.. ipython:: python
   :suppress:

   os.remove("foo.xlsx")

Gotchas
-------

If you are attempting to perform an operation you might see an exception like:

.. code-block:: python

    >>> if pd.Series([False, True, False]):
    ...     print("I was true")
    Traceback
        ...
    ValueError: The truth value of an array is ambiguous. Use a.empty, a.any() or a.all().

See :ref:`Comparisons<basics.compare>` for an explanation and what to do.

See :ref:`Gotchas<gotchas>` as well.
