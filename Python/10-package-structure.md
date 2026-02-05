# 第10章：パッケージ構造と import

**この章の目標**  
**パッケージ**の作り方と、**import** のルールを学びます。  
複数ファイルに分けてコードを整理する方法を、初心者向けに説明します。

---

## パッケージとは？

**パッケージ**は、**複数のモジュール（.py ファイル）をまとめたフォルダ**です。

- 機能ごとにファイルを分けると、管理しやすくなる  
- フォルダに **`__init__.py`** を置くと、そのフォルダがパッケージとして認識される  

---

## フォルダ構成の例

```
myproject/
├── main.py           ← エントリーポイント
├── utils/
│   ├── __init__.py   ← パッケージとして認識するためのファイル
│   ├── helpers.py
│   └── validators.py
└── models/
    ├── __init__.py
    └── user.py
```

---

## __init__.py とは？

**__init__.py** は、そのフォルダを**パッケージとして扱う**ためのファイルです。

- 中身が空でもよい（`__init__.py` が存在するだけでOK）  
- パッケージを import したときに実行されるコードを書くこともできる  

```python
# utils/__init__.py（空でよい場合）
# または、よく使う関数をここから export する場合
from .helpers import format_name
__all__ = ["format_name"]
```

---

## import の書き方

```python
# モジュールごと import
import utils.helpers
result = utils.helpers.format_name("山田")

# 特定の関数だけ import
from utils.helpers import format_name
result = format_name("山田")

# パッケージから import（__init__.py で export している場合）
from utils import format_name
```

---

## 相対 import と絶対 import

| 種類 | 例 | 意味 |
|------|-----|------|
| **絶対 import** | `from utils.helpers import foo` | プロジェクトのルートから見たパス |
| **相対 import** | `from .helpers import foo` | 同じパッケージ内の modules から |

- 同じパッケージ内なら `from .helpers` のように `.` で相対指定できる  

---

## まとめ

- **パッケージ**は `__init__.py` を置いたフォルダ。複数モジュールをまとめる。
- **import** でモジュールや関数を取り込む。絶対・相対の両方使える。
- 機能ごとにフォルダを分け、`__init__.py` で必要なものだけ export すると整理しやすい。
