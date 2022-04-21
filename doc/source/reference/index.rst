{{ header }}

.. _api:

=============
API リファレンス
=============

このページでは、
すべての public な pandas オブジェクト、関数、メソッドの概要を説明します。
pandas.* 名前空間で公開されているすべてのクラスと関数はパブリックです。

pandas.errors* 、pandas.plotting* 、pandas.testing* などのサブパッケージは公開されています。
pandas.io* と pandas.tseries* のサブモジュールのパブリック関数についてはドキュメントに記載されています。
pandas.api.types* サブパッケージは、pandas のデータ型に関するパブリック関数をいくつか保持しています。

.. 警告::

    pandas.core* 、pandas.compat* 、pandas.util* のトップレベルモジュールは、プライベートです。これらのモジュールの安定した機能は保証されていません。

.. If you update this toctree, also update the manual toctree in the
   main index.rst.template

.. toctree::
   :maxdepth: 2

   io
   general_functions
   series
   frame
   arrays
   indexing
   offset_frequency
   window
   groupby
   resampling
   style
   plotting
   general_utility_functions
   extensions

.. This is to prevent warnings in the doc build. We don't want to encourage
.. these methods.

..
    .. toctree::

        api/pandas.DataFrame.blocks
        api/pandas.DataFrame.as_matrix
        api/pandas.Index.asi8
        api/pandas.Index.data
        api/pandas.Index.flags
        api/pandas.Index.holds_integer
        api/pandas.Index.is_type_compatible
        api/pandas.Index.nlevels
        api/pandas.Index.sort
        api/pandas.Series.asobject
        api/pandas.Series.blocks
        api/pandas.Series.from_array
        api/pandas.Series.imag
        api/pandas.Series.real


.. Can't convince sphinx to generate toctree for this class attribute.
.. So we do it manually to avoid a warning

..
    .. toctree::

        api/pandas.api.extensions.ExtensionDtype.na_value
