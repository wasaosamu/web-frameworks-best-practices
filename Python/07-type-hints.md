# 第7章：型ヒント（Type Hints）

**この章の目標**  
**型ヒント**を使って、変数や関数の「型」を明示する書き方を学びます。  
Python ではオプションですが、書いておくと読みやすく、ミスも減りやすいです。

---

## 型ヒントとは？

**型ヒント**は、**変数や関数が「どんな型か」をコードで示す**ための書き方です。

- Python は動的型付けなので、型ヒントを書かなくても動く  
- 書いておくと、エディタの補完やエラー検出に役立つ  
- **実行時にはチェックされない**（あくまでヒント）  

---

## 変数に型を付ける

```python
# 変数名: 型 = 値
name: str = "山田"
age: int = 25
price: float = 99.9
is_active: bool = True
```

---

## 関数に型を付ける

```python
def add(a: int, b: int) -> int:
    return a + b

def greet(name: str) -> str:
    return f"こんにちは、{name} さん"
```

| 部分 | 意味 |
|------|------|
| `a: int` | 引数 a は int 型 |
| `-> int` | 戻り値は int 型 |

---

## リストと辞書の型

```python
from typing import List, Dict

# 文字列のリスト
names: List[str] = ["山田", "佐藤"]

# キーが str、値が int の辞書
scores: Dict[str, int] = {"山田": 80, "佐藤": 90}
```

- Python 3.9 以降では `list[str]`、`dict[str, int]` と書ける（小文字でOK）  

---

## オプショナル（None の可能性）

```python
from typing import Optional

def find_user(id: int) -> Optional[str]:
    # 見つかったら名前を返す、見つからなければ None
    if id == 1:
        return "山田"
    return None
```

- **Optional[str]** は `str | None` と同じ意味  

---

## まとめ

- **型ヒント**で、変数や関数の型を明示できる。実行時には強制されないが、補完やエラー検出に役立つ。
- 関数は `def 名前(引数: 型) -> 戻り値の型:` と書く。
- `List`、`Dict`、`Optional` などは `typing` からインポートする（3.9 以降は組み込み型も使える）。
