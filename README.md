# Python のダンダーな名前まとめ

Python では `__init__()` のように先頭と末尾がアンダースコア 2 つ（ `__` ）になっている名前に特別な意味を持つものがあり、それらは「ダンダー XXX 」（ dunder = double underscore ）と呼ばれたりしています。
ここではそのダンダーな名前についてまとめています。

細かな挙動については Python 公式のドキュメントや PEP やソースコードを参照してください。

最終確認時の Python バージョンは `3.9.x` です。

## 間違いを見つけたら

イシューで教えてください。

## 用語

説明をかんたんにするため各用語を次の意味で使います。

- モジュール: `.py` ファイル
- パッケージ（）: `__init__.py` ファイルを含むディレクトリ
- 標準ライブラリ: Python 本体に同梱のモジュールまたはパッケージ
- オブジェクト: クラスのインスタンス
- サブクラス: 他のクラスの子孫クラス
- アトリビュート: ドット記法（ `a.b` ）でアクセスできるオブジェクトの要素（日本語では「属性」と呼ばれたりもしています）

参考: [6. Modules — Python 3 documentation](https://docs.python.org/3/tutorial/modules.html)

関連記事: [Python にまつわるアイデア： Python のパッケージとモジュールの違い - Life with Python](https://www.lifewithpython.com/2018/05/python-difference-between-package-and-module.html)

## ディレクトリ名

### `__pycache__`

コンパイル後のモジュールのキャッシュ。

参考: [“Compiled” Python files | 6. Modules — Python 3 documentation](https://docs.python.org/3/tutorial/modules.html#compiled-python-files)

> To speed up loading modules, Python caches the compiled version of each module in the `__pycache__` directory under the name `module.version.pyc`, where the version encodes the format of the compiled file; it generally contains the Python version number. For example, in CPython release 3.3 the compiled version of spam.py would be cached as `__pycache__/spam.cpython-33.pyc`. This naming convention allows compiled modules from different releases and different versions of Python to coexist.

## ファイル名

### `__init__.py`

そのファイルが含まれるディレクトリを Python パッケージ化するためのファイル。
中にコードを書くとパッケージの初期化処理として実行される。

参考: [Packages | 6. Modules — Python 3 documentation](https://docs.python.org/3/tutorial/modules.html#packages)

> The `__init__.py` files are required to make Python treat directories containing the file as packages. This prevents directories with a common name, such as `string`, unintentionally hiding valid modules that occur later on the module search path. In the simplest case, `__init__.py` can just be an empty file, but it can also execute initialization code for the package or set the `__all__` variable, described later.

### `__main__.py`

モジュールが `python -m [モジュール名]` という形でスクリプトとして実行されたときに呼び出されるファイル。

## 標準ライブラリ

### `__future__`

Python 本体に互換性の無い変更があるときに新しい機能を提供するモジュール。

Python 3.9 時点で有効なのは `annotations` のみ。

例:

```python
from __future__ import annotations
```

参考: [`__future__` — Future statement definitions — Python 3 documentation](https://docs.python.org/3/library/__future__.html)

>`__future__` is a real module, and serves three purposes:
> 
> - To avoid confusing existing tools that analyze import statements and expect to find the modules they’re importing.
> - To ensure that future statements run under releases prior to 2.1 at least yield runtime exceptions (the import of `__future__` will fail, because there was no module of that name prior to 2.1).
> - To document when incompatible changes were introduced, and when they will be — or were — made mandatory. This is a form of executable documentation, and can be inspected programmatically via importing `__future__` and examining its contents.

## モジュール内トップレベル

参考: 

- [Import-related module attributes | 5. The import system — Python 3 documentation](https://docs.python.org/3/reference/import.html#import-related-module-attributes)
- [`importlib.abc.Loader.load_module` | `importlib` — The implementation of import — Python 3 documentation](https://docs.python.org/3/library/importlib.html#importlib.abc.Loader.load_module)

### `__name__`

そのモジュールの名前（ `str` ）。
他のモジュールから `import` されたときと `python` コマンドの引数で指定されて直接実行されたときで異なる。
他のモジュールから `import` されたときはファイルの basename （ファイル名から `.py` を除いた部分）、 `python` コマンドで直接実行されたときは `"__main__"` になる。

例:

```python
if __name__ == "__main__":
    import sys
    myfunc(sys.argv)
```

参考: [6. Modules — Python 3 documentation](https://docs.python.org/3/tutorial/modules.html#modules)

> A module is a file containing Python definitions and statements. The file name is the module name with the suffix .py appended. Within a module, the module’s name (as a string) is available as the value of the global variable `__name__`. For instance, use your favorite text editor to create a file called fibo.py in the current directory with the following contents:

参考: https://docs.python.org/3/tutorial/modules.html#executing-modules-as-scripts

> the code in the module will be executed, just as if you imported it, but with the `__name__` set to `"__main__"`. That means that by adding this code at the end of your module:

その他記事:

- [Python Tips：実行中のスクリプトの名前を取得したい - Life with Python](https://www.lifewithpython.com/2014/04/python-get-name-of-current-script.html)
- [Python Tips：ライブラリ・モジュールの場所を調べたい - Life with Python](https://www.lifewithpython.com/2014/07/python-get-location-of-libraries-modules-packages.html)

### `__file__`

モジュールファイルのフルパス（ `str` ）。
モジュールによっては（ C 言語で書かれたモジュール等では） `__file__` が存在しないこともある。

参考: [`__file__` | PEP 3147 -- PYC Repository Directories | Python.org](https://www.python.org/dev/peps/pep-3147/#file)

> In Python 3, when you import a module, its `__file__` attribute points to its source py file (in Python 2, it points to the pyc file). A package's `__file__` points to the py file for its `__init__.py`.

### `__cached__`

コンパイル後のモジュールのキャッシュのパス。
`__file__` が定義されていない場合は通常存在しない。

### `__path__`

パッケージのサブモジュール探索先のディレクトリ一覧（ `list` ）。
通常はモジュールには無くパッケージ（ `__init__.py` ファイル）にのみ含まれる。

### `__package__`

そのモジュールが含まれるパッケージの名前（ `str` ）。
パッケージ（ `__init__.py` ）の場合は `__name__` と同じになる。
セットされていない場合は `__spec__.parent` にフォールバックする。

### `__loader__`

モジュールをメモリに読み込むために使用されたローダーオブジェクト。

### `__dict__`

（読み込み専用）モジュールの名前空間に含まれる名前一覧（読み込み専用）（ `dict` ）。

### `__all__`

（書き込み専用） public な名前の一覧（ `list` ）。
`__all__` を定義すると、他の Python コードから `from [モジュール名] import *` で `import` されたとき `__all__` に含まれる名前のみが含まれる。
`__all__` を定義しなければ、先頭が `_ `で始まるもの以外のすべての名前が含まれる。

参考: [syntax - Can someone explain `__all__` in Python? - Stack Overflow](https://stackoverflow.com/questions/44834/can-someone-explain-all-in-python)

### `__doc__`

そのモジュールのドキュメンテーション文字列（ファイル内の最初の `""" ~ """` ）（ `str | None` ）。

### `__spec__`

モジュールのインポートに関する情報を集めた `importlib.machinery.ModuleSpec` クラスのインスタンス。
Python 3.4 で導入された比較的新しいもの。

### その他

モジュールそのものも `import` 後はオブジェクトとして扱えるので、通常のクラスと同じく以下のような特殊メソッドが（トップレベルの関数として）定義して使用できます。

- `__dir__()`
- `__getattr__()`

## クラス

### `__init__()` `__new__()`

オブジェクトが生成されるときの初期化処理を行う。
`__init__()` はオブジェクト生成後に呼び出される。
`__new__()` はオブジェクト生成の処理そのものとして呼び出される。

例:

```python
class Product:
    def __init__(self, id, name):
        self.id = ld
        self.name = name
```

### `__del__()`

オブジェクトが破棄されるときの処理を行う。
破棄はガーベッジコレクションによって行われるので `del` 文の後に座即に `__del__()` が呼ばれるとはかぎらない。

### `__getattr__()` `__setattr__()` `__delattr__()` `__getattribute__()`

ドット記法（ `a.b` ）で（または `getattr()` `setattr()` `delattr()` で）オブジェクトのアトリビュートにアクセスされたときの処理を行う。
`__getattr__()` は `a.b` と参照されたとき、 `__setattr__()` は `a.b = c` と代入されたとき、 `__delattr__()` は `del a.b` と削除されたときの処理を担う。

`__getattr__()` と `__getattribute__()` はともに参照時の処理を行うが次の違いがある。

- `__getattr__()`: 未定義のアトリビュートが参照されたときに呼び出される。
- `__getattribute__()`: ドット記法が使われたときに必ず（アトリビュートが未定義かどうかによらず）呼び出される。

参考: [Customizing module attribute access | 3. Data model — Python 3 documentation](https://docs.python.org/3/reference/datamodel.html#customizing-module-attribute-access)

### `__dir__()` 

`dir(object)` の実装として使用される。 

### `__get__()` `__set__()` `__delete__()` `__set_name__()`

デスクリプタ（ descriptor ）プロトコルと呼ばれるもので、アトリビュートを再利用できる仕組みを提供する。
定義された場合、そのクラスのオブジェクトが別のクラス（「オーナークラス」）のクラスアトリビュートに格納されたときに、オーナークラスのオブジェクトのアトリビュート周りの実装として使われる。
`__set_name__()` はオーナークラスが作られたときに呼ばれる。
`__get__()` `__set__()` `__delete__()` はオーナークラスのオブジェクトのアトリビュート（またはオーナークラス自身のアトリビュート）にアクセスされたときに呼ばれる。
`__get__()` は参照時、 `__set__()` は代入時、 `__delete__()` は削除時に呼ばれる。

例:

```python
from weakref import WeakKeyDictionary


class UniqueField:
    """値がユニークなフィールド

    2 つの異なるインスタンスで同じ値をセットしようとするとエラーになる
    """

    def __set_name__(self, owner, name):
        self.name = name
        self.values = WeakKeyDictionary()

    def __set__(self, instance, value):
        if value in self.values.values():
            raise ValueError(f"Value must be unique: {value}")
        self.values[instance] = value

    def __get__(self, instance, owner=None):
        # クラスからアクセスされたとき
        if instance is None:
            return self
        # インスタンスからアクセスされたとき
        return self.values[instance]

    def __delete__(self, instance):
        self.values.pop(instance)


class Product:
    # ここで `__set_name__()` が呼ばれる
    id = UniqueField()


p1, p2 = Product(), Product()
p1.id = "P01"
p2.id = "P01"
# => ValueError: Value must be unique: P01
```

参考: [Implementing Descriptors | 3. Data model — Python 3 documentation](https://docs.python.org/3/reference/datamodel.html#implementing-descriptors)

参考: [Descriptor HowTo Guide — Python 3 documentation](https://docs.python.org/3/howto/descriptor.html)

### `__slots__()`

定義された場合、オブジェクトに所持させられるデータメンバーの名前を制限する。

参考: [`__slots__` | 3. Data model — Python 3 documentation](https://docs.python.org/3/reference/datamodel.html#slots)

### `__getitem__()` `__setitem__()` `__delitem__()` `__missing__()`

ブラケット記法（ `a[b]` ）が使われたときの処理を行う。
`__getitem__()` は `a[b]` と参照されたとき、 `__setitem__()` は `a[b] = c` と代入されたとき、 `__delitem__()` は `del a[b]` と削除されたときの処理を担う。
`__missing__()` は `dict.__getitem__()` において指定されたキーが存在しないときに呼ばれるもので、 `dict` のサブクラスにおいて有用。

### `__call__()`

オブジェクトが `a()` と関数のように（ `Callable` として）呼び出されたときに処理を行う。

例:

```python
class Printer:
    def __init__(self, prefix: str):
        self.prefix = prefix

    def __call__(self, *args):
        print(self.prefix, *args)


p1 = Printer('[info]')
p1('Hello', 'world')
# => [info] Hello world
```

### `__hash__()`

定義された場合、 `hash(object)` の実装として利用される。
`hash()` の戻り値は `dict` のキーや `set` の要素の同一性判定に使用される。

### `__repr__()` `__str__()` `__format__()`

オブジェクトを文字列（ `str` ）化するときの実装として利用される。
それぞれ、 `__repr__()` は `repr(object)` の、 `__str__()` は `str(object)` の、 `__format__()` は `format(value, [format_spec])` の実装を担う。 
`__format__()` はフォーマット済み文字列リテラル（ `f""` ）や `str.format()` でも使用される。

### `__bytes__()`

`bytes(object)` の実装として利用される。

### `__bool__()` `__and__()` `__or__()`

オブジェクトが真偽値として扱われたときの実装として利用される。
`__bool__()` は `bool(object)` の、 `__and__()` と `__or__()` は `and` `or` 演算子で利用されたときの実装を担う。
`if` 文の評価には `bool(object)` の戻り値が使われる。

例:

```python
class Array:
    def __init__(self, items):
        self.items = items

    def __bool__(self):
        return len(self.items) > 0


if Array([]):
    print('Filled')
else:
    print('Empty')
# => Empty

if Array([3]):
    print('Filled')
else:
    print('Empty')
# => Filled
```

### `__eq__()` `__ne__()` `__lt__()` `__le__()` `__gt__()` `__ge__()`

比較演算子の処理の実装として利用される。

| メソッド | 演算子 |
| --- | --- |
| `__eq__()` | `==` |
| `__ne__()` | `!=` |
| `__lt__()` | `<` |
| `__le__()` | `<=` |
| `__gt__()` | `>` |
| `__ge__()` | `g=` |

### `__add__()` `__sub__()` `__mul__()` `__matmul__()` `__truediv__()` `__floordiv__()` `__mod__()` `__divmod__()` `__pow__()` `__lshift__()` `__rshift__()` `__and__()` `__xor__()` `__or__()`

二項算術演算子の実装として利用される。
演算子の左で使われたときに（ `x + y` なら `x` において）呼び出される。

| メソッド | 演算子 |
| --- | --- |
| `__add__()` | `+` |
| `__sub__()` | `-` |
| `__mul__()` | `*` |
| `__matmul__()` | `@` |
| `__truediv__()` | `/` |
| `__floordiv__()` | `//` |
| `__mod__()` | `%` |
| `__divmod__()` | `divmod()` |
| `__pow__()` | `pow()` `**` |
| `__lshift__()` | `<<` |
| `__rshift__()` | `>>` |
| `__and__()` | `&` |
| `__xor__()` | `^` |
| `__or__()` | `|` |

### `__radd__()` `__rsub__()` `__rmul__()` `__rmatmul__()` `__rtruediv__()` `__rfloordiv__()` `__rmod__()` `__rdivmod__()` `__rpow__()` `__rlshift__()` `__rrshift__()` `__rand__()` `__rxor__()` `__ror__()`

二項算術演算子の実装として利用される。
演算子の右で使われたときに、演算子の左の要素が二項算術演算子の実装を持っていない場合に呼び出される。

### `__iadd__()` `__isub__()` `__imul__()` `__imatmul__()` `__itruediv__()` `__ifloordiv__()` `__imod__()` `__ipow__()` `__ilshift__()` `__irshift__()` `__iand__()` `__ixor__()` `__ior__()`

augmented arithmetic assignments （算術演算子と代入演算子が組み合わさったもの）の実装として利用される。

| メソッド | 演算子 |
| --- | --- |
| `__iadd__()` | `+=` |
| `__isub__()` | `-=` |
| `__imul__()` | `*=` |
| `__imatmul__()` | `@=` |
| `__itruediv__()` | `/=` |
| `__ifloordiv__()` | `//=` |
| `__imod__()` | `%=` |
| `__ipow__()` | `**=` |
| `__ilshift__()` | `<<=` |
| `__irshift__()` | `>>=` |
| `__iand__()` | `&=` |
| `__ixor__()` | `^=` |
| `__ior__()` | `|=` |

### `__neg__()` `__pos__()` `__abs__()` `__invert__()`

単項算術演算子の実装として利用される。

| メソッド | 演算子 |
| --- | --- |
| `__neg__()` | `-` |
| `__pos__()` | `+` |
| `__abs__()` | `abs()` |
| `__invert__()` | `~` |

### `__complex__()` `__int__()` `__float__()`

数値の型変換の関数の実装として利用される。

| メソッド | 関数 |
| --- | --- |
| `__complex__()` | `complex()` |
| `__int__()` | `int()` |
| `__float__()` | `float()` |

### `__round__()` `__trunc__()` `__floor__()` `__ceil__()`

数値の丸め込み・切り捨ての実装として利用される。

| メソッド | 関数 |
| --- | --- |
| `__round__()` | `round()` |
| `__trunc__()` | `trunc()` |
| `__floor__()` | `floor()` |
| `__ceil__()` | `ceil()` |


## 参考

- [6. Modules — Python 3 documentation](https://docs.python.org/3/tutorial/modules.html)
- [3. Data model — Python 3 documentation](https://docs.python.org/3/reference/datamodel.html)
- [Built-in Functions — Python 3 documentation](https://docs.python.org/3/library/functions.html)

