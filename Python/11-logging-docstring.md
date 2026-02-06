# 第11章：ロギングと docstring

**この章の目標**  
**ロギング**（ログ出力）と **docstring**（関数などの説明文）の書き方を学びます。  
デバッグや保守をしやすくするための基本を、初心者向けに説明します。

---

## ロギングとは？

**ロギング**は、**プログラムの動きを記録に残す**仕組みです。

- **print** の代わりに、**logging** を使うと、レベル（重要度）を付けられる  
- 本番では print を消し忘れる心配が減る  
- いつ・何が起きたかを後から確認しやすくなる  

---

## ロギングの基本

```python
import logging                        # 標準ライブラリの logging をインポート

# レベルを設定（DEBUG 以上を出力する）
logging.basicConfig(level=logging.INFO)  # INFO レベル以上を出力するよう設定

logging.debug("詳細な情報")    # DEBUG: デバッグ用（INFO より下なので通常は出ない）
logging.info("通常の情報")     # INFO: 通常の情報
logging.warning("警告")        # WARNING: 警告メッセージ
logging.error("エラー")        # ERROR: エラーメッセージ
```

| レベル | 意味 |
|--------|------|
| **DEBUG** | デバッグ用の詳細 |
| **INFO** | 通常の情報 |
| **WARNING** | 警告（動くが注意が必要） |
| **ERROR** | エラー |

---

## docstring とは？

**docstring** は、**関数やクラスの説明文**です。`"""` で囲んで書きます。

```python
def add(a: int, b: int) -> int:
    """2つの数値を足して返す。

    Args:
        a: 1つ目の数
        b: 2つ目の数

    Returns:
        2つの数の合計
    """
    return a + b
```

- 関数の直下に書く  
- `help(add)` やエディタの補完で表示される  
- チーム開発では、何をする関数か伝えるのに役立つ  

---

## docstring の書き方（シンプル版）

```python
def greet(name: str) -> str:
    """名前を受け取り、挨拶文を返す。"""
    return f"こんにちは、{name} さん"  # f文字列で変数を埋め込んだ文字列を返す
```

- 1行で書く場合も、3行の `"""` で囲む  

---

## まとめ

- **logging** でログを出力する。print の代わりに使うと、レベル分けや後からの確認がしやすい。
- **docstring** で関数の説明を書く。`"""` で囲み、関数の直下に置く。
