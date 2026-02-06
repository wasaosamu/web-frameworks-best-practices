# pytest 入門

**この章の目標**  
**pytest** を使って、テストを書く方法を学びます。  
Python で最も人気のあるテストフレームワークです。

---

## pytest とは？

**pytest** は、**テストを書くためのライブラリ**です。

- **シンプル**で、`assert` をそのまま使える  
- `test_` で始まる関数が自動でテストとして実行される  
- unittest より少ないコードでテストが書ける  

---

## インストール

```bash
pip install pytest           # pytest を pip でインストール
```

---

## 基本的なテストの書き方

```python
# test_calc.py
def add(a: int, b: int) -> int:   # テスト対象の関数
    return a + b

def test_add_positive():          # test_ で始まる関数がテストとして実行される
    assert add(2, 3) == 5         # assert で期待値を検証

def test_add_zero():
    assert add(0, 5) == 5
```

- **test_** で始まる関数がテストとして認識される  
- **assert 条件** で、条件が偽ならテスト失敗  

---

## 実行方法

```bash
pytest test_calc.py          # 指定ファイルのテストを実行
pytest                       # カレントフォルダの test_*.py をすべて実行
pytest -v                    # 詳細表示（-v オプション）
```

---

## 失敗時の表示

```python
def test_add():
    assert add(2, 3) == 6    # 実際は 5 なので失敗
```

- 失敗すると、期待値と実際の値が表示される  
- どの行で失敗したかも分かる  

---

## まとめ

- **test_** で始まる関数を書く。
- **assert** で期待値を検証する。
- **pytest** コマンドでテストを実行する。
