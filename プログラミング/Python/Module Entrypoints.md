---
title: Module Entrypoints
created: 2025-09-29 14:39:32
updated: 2025-09-29 15:08:03
tags:
  - Python
---

# Python モジュールとエントリーポイント: `__init__.py` と `__main__.py`

Python のパッケージ開発において、`__init__.py` と `__main__.py` は特別な意味を持つファイルです。これらはパッケージの構造と振る舞いを定義し、モジュールのインポートやパッケージの直接実行を制御します。

## 1. `__init__.py` の役割

`__init__.py` ファイルは、ディレクトリを Python パッケージとして認識させるために不可欠なファイルです。このファイルが存在することで、Python インタープリタはそのディレクトリをモジュールの集合体（パッケージ）として扱います。

### 1.1. パッケージの識別

`__init__.py` が存在しないディレクトリは、単なる通常のディレクトリとして扱われ、その中のモジュールを `import` する際に問題が発生する可能性があります。

Python 3.3 以降では、`__init__.py` がなくてもパッケージとして扱われる「名前空間パッケージ (Namespace Packages)」という概念がありますが、一般的なパッケージでは依然として `__init__.py` が必要です。

### 1.2. パッケージの初期化コード

パッケージが `import` されたときに、`__init__.py` 内のコードが一度だけ実行されます。

この特性を利用して、パッケージレベルの初期化処理（例: ロガーの設定、設定ファイルの読み込み、サブモジュールのインポートなど）を行うことができます。

```python
# mypackage/__init__.py
print("mypackage が初期化されました")

VERSION = "1.0.0"

from . import submodule_a
from . import submodule_b
```

### 1.3. `__all__`によるインポート制御

`__init__.py` 内で `__all__` というリストを定義することで、`from mypackage import *` のようにワイルドカードインポートが行われた際に、どのモジュールや変数がインポートされるかを制御できます。

```python
# mypackage/__init__.py
__all__ = ["submodule_a", "VERSION"] # submodule_b はインポートされない
```

### 1.4. 相対インポートの起点

パッケージ内のモジュール間で相対インポート（例: `from . import another_module` や `from .. import parent_module`）を行う際の起点となります。

**例:**

以下のディレクトリ構造を考えます。

```
mypackage/
├── __init__.py
├── module_a.py
└── subpackage/
    ├── __init__.py
    └── module_b.py
```

`mypackage/module_a.py` から `mypackage/subpackage/module_b.py` をインポートする場合

```python
# mypackage/module_a.py
from .subpackage import module_b

def func_a():
    print("module_a の関数")
    module_b.func_b()
```

`mypackage/subpackage/module_b.py` から `mypackage/module_a.py` をインポートする場合

```python
# mypackage/subpackage/module_b.py
from .. import module_a # 親パッケージの module_a をインポート

def func_b():
    print("module_b の関数")
    module_a.func_a()
```

## 2. `__main__.py` の役割

`__main__.py` ファイルは、パッケージを通常の Python スクリプトのように直接実行可能にするための特別なモジュールです。

### 2.1. パッケージの直接実行

`__main__.py` が含まれるパッケージは、以下のコマンドで直接実行できます。

```bash
python -m mypackage
```

このコマンドが実行されると、Python インタープリタは `mypackage/__main__.py` ファイルを見つけて実行します。

### 2.2. `if __name__ == "__main__":`との違い

通常の Python スクリプトでは、`if __name__ == "__main__":` ブロック内に、そのスクリプトが直接実行されたときにのみ実行したいコードを記述します。

`__main__.py` も同様に、このブロック内にパッケージが直接実行されたときの処理を記述するのが一般的です。

```python
# mypackage/__main__.py
from . import some_module

def main():
    print("mypackage が直接実行されました！")
    some_module.run_logic()

if __name__ == "__main__":
    main()
```

### 2.3. ユースケース

- **CLI ツール**: パッケージ全体をコマンドラインツールとして提供する場合に便利です。ユーザーは `python -m mypackage` でツールを実行できます。
- **単一ファイルで実行可能なパッケージ**: パッケージ内に複数のモジュールがあっても、`__main__.py` をエントリーポイントとして提供することで、ユーザーは単一のコマンドでパッケージの主要な機能を利用できます。
