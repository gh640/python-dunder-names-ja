# Python のダンダーな名前まとめ

Python には `__init__()` のように「先頭と末尾がアンダースコア 2 つ（ `__` ）」で「特別な意味を持つもの」があります。
それらは「ダンダー XXX 」（ dunder = double underscore ）や「特殊 XXX 」「マジック XXX 」と呼ばれたりします（ XXX には「アトリビュート」「メソッド」などが入ります）。

ここではそれらのダンダーな名前についてまとめています。

各項目の説明はあくまでも概説なので、厳密な仕様・挙動については Python 公式のドキュメントや PEP やソースコードで確認してください。

動作確認に使用した Python バージョンは `3.9.x` です。

## 目次

- [間違いを見つけたら](#間違いを見つけたら)
- [用語](#用語)
- [ディレクトリ名](#ディレクトリ名)
- [ファイル名](#ファイル名)
- [標準ライブラリ](#標準ライブラリ)
- [モジュール内トップレベル](#モジュール内トップレベル)
- [クラス](#クラス)
- [関数](#関数)
- [メソッド](#メソッド)
- [オブジェクト](#オブジェクト)
- [組み込み定数](#組み込み定数)
- [組み込み関数](#組み込み関数)
- [参考](#参考)

## 間違いを見つけたら

イシューで教えてください。

## 用語

説明をかんたんにするため各用語を次の意味で使います。

- モジュール: `.py` ファイル
- パッケージ: `__init__.py` ファイルを含むディレクトリ
- 標準ライブラリ: Python 本体に同梱のモジュールまたはパッケージ
- オブジェクト: クラスのインスタンス
- サブクラス: 他のクラスの子孫クラス
- ベースクラス: 他のクラスの祖先クラス
- アトリビュート: ドット記法（ `a.b` ）でアクセスできるオブジェクトの要素（日本語では「属性」と呼ばれたりもします）
- MRO: Method Resolution Order 。メソッド解決順序。
- 弱参照: weak references 。ガーベッジコレクションによる回収を阻まない弱い参照。

参考: [6. Modules — Python 3 documentation](https://docs.python.org/3/tutorial/modules.html)

関連記事: [Python にまつわるアイデア： Python のパッケージとモジュールの違い - Life with Python](https://www.lifewithpython.com/2018/05/python-difference-between-package-and-module.html)

## ディレクトリ名

### `__pycache__`

コンパイル後のモジュールのキャッシュ。

参考: [“Compiled” Python files | 6. Modules — Python 3 documentation](https://docs.python.org/3/tutorial/modules.html#compiled-python-files)

> To speed up loading modules, Python caches the compiled version of each module in the `__pycache__` directory under the name `module.version.pyc`, where the version encodes the format of the compiled file; it generally contains the Python version number. For example, in CPython release 3.3 the compiled version of spam.py would be cached as `__pycache__/spam.cpython-33.pyc`. This naming convention allows compiled modules from different releases and different versions of Python to coexist.

### `sys.__stdin__` `sys.__stdout__` `sys.__stderr__`

差し替え可能な標準入力・標準出力・標準エラーのオリジナルの値を保持する。

| アトリビュート | フック関数 |
| --- | --- |
| `sys.__stdin__` | `sys.stdin` |
| `sys.__stdout__` | `sys.stdout` |
| `sys.__stderr__` | `sys.stderr` |

### `sys.__breakpointhook__` `sys.__displayhook__` `sys.__excepthook__` `sys.__unraisablehook__`

差し替え可能なフック関数のオリジナルの実装を保持する。
差し替え後に元に戻したいときに利用できる。

| アトリビュート | フック関数 |
| --- | --- |
| `sys.__breakpointhook__` | `sys.breakpointhook()` |
| `sys.__displayhook__` | `sys.displayhook()` |
| `sys.__excepthook__` | `sys.excepthook()` |
| `sys.__unraisablehook__` | `sys.unraisablehook()` |

参考: [`sys.__breakpointhook__` | sys — System-specific parameters and functions — Python 3 documentation](https://docs.python.org/3/library/sys.html#sys.__breakpointhook__)

### `sys.__interactivehook__`

定義された場合、 Python インタプリタがインタラクティブモードで起動するときに呼び出される。

参考: [`sys.__interactivehook__` | sys — System-specific parameters and functions — Python 3 documentation](https://docs.python.org/3/library/sys.html#sys.__interactivehook__)

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

他のモジュールから `import` されたときと `python` コマンドで直接指定されて実行されたときで異なる。
前者の場合はファイルの basename （ファイル名から `.py` を除いた部分）、後者の場合は `"__main__"` になる。

例:

```python
if __name__ == "__main__":
    import sys
    myfunc(sys.argv)
```

参考: [6. Modules — Python 3 documentation](https://docs.python.org/3/tutorial/modules.html#modules)

> A module is a file containing Python definitions and statements. The file name is the module name with the suffix `.py` appended. Within a module, the module’s name (as a string) is available as the value of the global variable `__name__`. For instance, use your favorite text editor to create a file called `fibo.py` in the current directory with the following contents:

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

コンパイル後のモジュールのキャッシュのパス（ `str` ）。
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

（読み込み専用）モジュールの名前空間に含まれる名前一覧（ `dict` ）。

### `__all__`

（書き込み専用）外部に公開する public な名前を明示的に指定したいときに使用できる（ `list` ）。

`__all__` を定義すると、他の Python コードから `from [モジュール名] import *` で `import` されたとき `__all__` に名前が含まれる要素のみがさらされる。
`__all__` を定義しない場合は、先頭が `_ `で始まるもの以外のすべての名前が含まれる。

参考: [syntax - Can someone explain `__all__` in Python? - Stack Overflow](https://stackoverflow.com/questions/44834/can-someone-explain-all-in-python)

### `__doc__`

そのモジュールのドキュメンテーション文字列（ファイル内の最初の `""" ~ """` ）（ `str | None` ）。

### `__spec__`

モジュールのインポートに関する情報を集めた `importlib.machinery.ModuleSpec` クラスのインスタンス。
Python 3.4 で導入された比較的新しいもの。

### その他

モジュールそのものもオブジェクトとして扱えるので、通常のオブジェクトと同じく以下のような特殊メソッドが（トップレベルの関数として）定義して使用できます。

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


p1 = Product('DANGO-001', 'だんご')
```

### `__del__()`

オブジェクトが破棄されるときの処理を行う。
破棄はガーベッジコレクションによって行われるので `del` 文で座即に `__del__()` が呼ばれるとはかぎらない。

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

データデスクリプタ（ data descriptor ）の場合は（＝ `__set__()` または `__delete__()` を持つ場合は）、 `__set_name__()` の中で `__objclass__` アトリビュートをセットしておくと、それが `inspect` での利用先クラスの特定に役立つ。

参考: 

- [Implementing Descriptors | 3. Data model — Python 3 documentation](https://docs.python.org/3/reference/datamodel.html#implementing-descriptors)
- [Descriptor HowTo Guide — Python 3 documentation](https://docs.python.org/3/howto/descriptor.html)

### `__getitem__()` `__setitem__()` `__delitem__()` `__missing__()`

ブラケット記法（ `a[b]` ）が使われたときの処理を行う。
`__getitem__()` は `a[b]` と参照されたとき、 `__setitem__()` は `a[b] = c` と代入されたとき、 `__delitem__()` は `del a[b]` と削除されたときの処理を担う。
`__missing__()` は `dict.__getitem__()` において指定されたキーが存在しないときに呼ばれるもので、 `dict` のサブクラスにおいて有用。

### `__len__()` `__length_hint__()`

コンテナ型のオブジェクトにおいて要素数を返す関数の実装として使用される。
`__len__()` は `len(object)` の、 `__length_hint__()` は `operator.length_hint(object)` の実装になる。
`__bool__()` を持たず `__len__()` を持つクラスにおいては `__len__()` が 0 を返すときに `bool(object)` が `False` を返すようになる。

### `__iter__()` `__next__()`

イテレーターの実装として使用される。

コンテナ型のクラスで `__iter__()` 定義されそれが iterator なオブジェクトを返す場合、クラスのオブジェクトは `for` 文などで使える iterable になる。
戻り値の iterator は `__iter__()` と `__next__()` を持つ必要がある。

コンテナ型のクラスにおいて `__iter__()` をジェネレーターとして定義した場合も同様にそのクラスのオブジェクトは iterable になる。

例:

```python
class Array:
    def __init__(self, items):
        self.items = items

    def __iter__(self):
        return iter(self.items)


a1 = Array(['青', '椒', '肉', '絲'])        
for c in a1:
    print(c)
# =>
# 青
# 椒
# 肉
# 絲
```

参考:

- [iterable | Glossary — Python 3 documentation](https://docs.python.org/3/glossary.html#term-iterable)
- [iterator | Glossary — Python 3 documentation](https://docs.python.org/3/glossary.html#term-iterator)
- [Iterator Types | uilt-in Types — Python 3 documentation](https://docs.python.org/3/library/stdtypes.html#iterator-types)

### `__reversed__()`

`reversed(object)` の実装として利用される。

### `__contains__()`

`in` 演算の実装として利用される。

例:

```python
class Dish:
    def __init__(self, ingredients):
        self.ingredients = ingredients

    def __contains__(self, item):
        return item in self.ingredients


takoyaki = Dish(['たこ', 'もち', 'キムチ'])

'もち' in takoyaki  # => True
'梅干し' in takoyaki  # => False
```

### `__call__()`

オブジェクトが `a()` と関数のように（ `Callable` として）呼び出されたときの実装として利用される。

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

`__call__()` が定義されているクラスのオブジェクトは `callable(object)` で `True` を返す。

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

### `__index__()`

`opertor.index()` の実装として利用される。
また、数値オブジェクトを `int` オブジェクトに変換する際にも利用される。
`complex()` `int()` `float()` はそれぞれ `__complex__()` `__int__()` `__float__()` が定義されていない場合は代わりに `__index__()` を利用する。

### `__round__()` `__trunc__()` `__floor__()` `__ceil__()`

数値の丸め込み・切り捨ての実装として利用される。

| メソッド | 関数 |
| --- | --- |
| `__round__()` | `round()` |
| `__trunc__()` | `math.trunc()` |
| `__floor__()` | `math.floor()` |
| `__ceil__()` | `math.ceil()` |

### `__enter__()` `__exit__()`

オブジェクトがコンテキストマネージャとして（ `with` 文で）使われたときの実装として利用される。

参考: 

- [With Statement Context Managers | 3. Data model — Python 3 documentation](https://docs.python.org/3/reference/datamodel.html#with-statement-context-managers)
- [PEP 343 -- The "with" Statement | Python.org](https://www.python.org/dev/peps/pep-0343/)

### `__await__()`

`await` キーワードといっしょに使える awaitable なオブジェクトの実装として利用される。
戻り値には iterator を返す必要がある。
Python 3.5 で追加された比較的新しいもの。

参考:

- [Coroutines | 3. Data model — Python 3 documentation](https://docs.python.org/3/reference/datamodel.html#coroutines)
- [PEP 492 -- Coroutines with async and await syntax | Python.org](https://www.python.org/dev/peps/pep-0492/)

### `__aiter__()` `__anext__()`

`async for` 等で使える非同期版 iterator の実装として利用される。

### `__aenter__()` `__aexit()`

`async with` で使える非同期版コンテキストマネージャの実装として利用される。

### `__getnewargs_ex__()` `__getnewargs__()` `__getstate__()` `__setstate__()` `__reduce__()` `__reduce_ex__()`

標準ライブラリ `pickle` によるオブジェクトの保存・復元に使用される。

参考: [pickle — Python object serialization — Python 3.9.7 documentation](https://docs.python.org/3/library/pickle.html#pickling-class-instances)

### `__subclasses__()`

そのクラスのサブクラスの弱参照。

### `__instancecheck__()`

定義された場合、 `instance(instance, class)` の実装として利用される。

参考: [Customizing instance and subclass checks | 3. Data model — Python 3 documentation](https://docs.python.org/3/reference/datamodel.html#customizing-instance-and-subclass-checks)

### `__subclasscheck__()`

定義された場合、 `issubclass(subclass, class)` の実装として利用される。

### `__subclasshook__()`

Abstract Base Class （ `abc.ABC` のサブクラス）で定義された場合、 `issubclass(subclass, class)` の実装として利用される。
`True` `Flase` `NotImplemented` のうちいずれかを返す必要がある。
`NotImplemented` を返した場合は通常のサブクラスチェック処理が走る。
正確には `abc.ABCMeta` の `__subclasscheck__` の中で呼ばれる。

参考: [`abc` —  Abstract Base Classes — Python 3 documentation](https://docs.python.org/3/library/abc.html#abc.ABCMeta.__subclasshook__)

### `__init_subclass__()`

クラス版の `__init__()` 。
サブクラスが作られるときの初期化処理を行う。

参考: [PEP 487 -- Simpler customisation of class creation | Python.org](https://www.python.org/dev/peps/pep-0487/)

### `__class_getitem__()` `__mro_entries__()`

ジェネリックな型を定義するための方法を提供する。

`__class_getitem__()` はクラス版の `__getitem__()` 。
クラスに対してブラケット記法（ `A[b]` ）が使われたときの実装として利用される。

`__mro_entries__()` は、これを定義したクラスのオブジェクトが他のクラスの `__bases__` に含まれていた場合、 `__bases__` と `__mro__` を書き換えることができる。
書き換え前の `__bases__` は `__orig_bases__` として保持される。

Python 3.7 で導入された比較的新しいもの。

例（ PEP 560 より）:

```python
class GenericAlias:
    def __init__(self, origin, item):
        self.origin = origin
        self.item = item
    def __mro_entries__(self, bases):
        return (self.origin,)

class NewList:
    def __class_getitem__(cls, item):
        return GenericAlias(cls, item)

class Tokens(NewList[int]):
    ...

Tokens.__bases__  # => (NewList,)
Tokens.__orig_bases__  # => (NewList[int],)
Tokens.__mro__  # => (Tokens, NewList, object)
```

参考: 

- [Emulating generic types | 3. Data model — Python 3 documentation](https://docs.python.org/3/reference/datamodel.html#emulating-generic-types)
- [PEP 560 -- Core support for typing module and generic types | Python.org](https://www.python.org/dev/peps/pep-0560/)

### `__slots__`

定義された場合、オブジェクトに所持させられるデータメンバー（≒アトリビュート）の名前を制限する。
`__slots__` が定義されたクラスでは `__dict__` と `__weakref__` の自動生成が行われないため、利用リソースの削減とルックアップスピード向上の効果もある。

参考: [`__slots__` | 3. Data model — Python 3 documentation](https://docs.python.org/3/reference/datamodel.html#slots)

### `__bases__` `__mro__` `__doc__` `__name__` `__qualname__` `__dict__` `__weakref__`

`class` キーワードを使って定義されたクラスに自動的に追加されるアトリビュート。

| 名前 | 型 | 意味 |
| --- | --- | --- |
| `__bases__` | `tuple` | ベースクラス |
| `__mro__` | `tuple` | メソッド呼び出しが行われたときの実装探索先クラス |
| `__doc__` | `str | None` | クラスのドキュメンテーション文字列 |
| `__name__` | `str` | クラス名 |
| `__qualname__` | `str` | クラスの qualified name |
| `__dict__` | `mappingproxy` | アトリビュートの保持用のスペース |
| `__weakref__` | `getset_descriptor` | オブジェクトへの弱参照のリスト |

## 関数

### `__doc__` `__name__`  `__qualname__` `__module__` `__defaults__` `__code__` `__globals__` `__dict__` `__closure__` `__annotations__` `__kwdefaults__`

`def` キーワードを使って定義された関数（「ユーザー定義関数」）に自動的に追加されるアトリビュート。

| 名前 | 型 | 意味 |
| --- | --- | --- |
| `__doc__`  | `str | None` | 関数のドキュメンテーション文字列 |
| `__name__`  | `str` | 関数の名前 |
| `__qualname__`  | `str` | 関数の qualified name |
| `__module__`  | `str` | 定義されたモジュール名 |
| `__defaults__`  | `tuple | None` | 引数のデフォルト値 |
| `__code__`  | `code` | コンパイルされた関数のボディ |
| `__globals__` | `dict` | グローバル変数への参照 |
| `__dict__` | `dict` | アトリビュート保持用のスペース |
| `__closure__` | `tuple | None` | 自由変数に対する bindings のセル |
| `__annotations__`  | `dict` | 引数のアノテーション |
| `__kwdefaults__` | `dict | None` | キーワード引数のデフォルト値 |

## メソッド

### `__self__`

ひもづけられたオブジェクト（ OOP 用語でいう「 message receiver 」）。

### その他

関数と同じアトリビュートが自動的に追加される。

## オブジェクト

### `__dict__`

アトリビュートを保持するためのスペース（ `dict` ）。

### `__class__`

そのオブジェクトが所属するクラス。

参考: [Special Attributes | Built-in Types — Python 3 documentation](https://docs.python.org/3/library/stdtypes.html#special-attributes)

### `__sizeof__`

オブジェクトのメモリ消費量を調べる `sys.getsizeof(object)` の実装として使用される。

参考: [`sys.getsizeof()` | `sys` — System-specific parameters and functions — Python 3 documentation](https://docs.python.org/3/library/sys.html#sys.getsizeof)

## 組み込み定数

### `__debug__`

`python` コマンドがオプション `-O` を付けて実行された場合は `True` 、その他の場合は `False` 。
環境変数 `PYTHONOPTIMIZE` でも `-O` と同様の制御ができる。

参考: 

- [`__debug__` | Built-in Constants — Python 3 documentation](https://docs.python.org/3/library/constants.html#__debug__)
- [`-O` | 1. Command line and environment — Python 3 documentation](https://docs.python.org/3/using/cmdline.html#cmdoption-o)

## 組み込み関数

### `__import__()`

`import` 文が使われたときに呼び出される。

実体は `importlib.__import__()` 。
`__import__()` の使用は非推奨で、モジュールの動的なインポートには `importlib.import_module()` を使うことが推奨されている。

参考: 

- [`__import__()` | Built-in Functions — Python 3 documentation](https://docs.python.org/3/library/functions.html#__import__)
- [`importlib.__import__()` | importlib — The implementation of import — Python 3.9.7 documentation](https://docs.python.org/3/library/importlib.html#importlib.__import__)

## 参考

公式ドキュメントでダンダーアトリビュートやダンダーメソッドによく言及しているページ:

- [3. Data model — Python 3 documentation](https://docs.python.org/3/reference/datamodel.html)
- [Built-in Functions — Python 3 documentation](https://docs.python.org/3/library/functions.html)
- [Type Objects — Python 3 documentation](https://docs.python.org/3/c-api/typeobj.html)
- [6. Modules — Python 3 documentation](https://docs.python.org/3/tutorial/modules.html)
