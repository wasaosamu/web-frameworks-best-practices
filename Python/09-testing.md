# 第9章：テスト（pytest・unittest）

**この章の目標**  
Python で**テストを書く**方法を学びます。  
pytest と unittest の基本を、初心者向けに説明します。

---

## テストとは？

**テスト**は、**プログラムが正しく動くか確認する**ためのコードです。

- 関数に「こういう入力をしたら、こういう結果になるはず」を書く  
- 実行すると、期待どおりかどうかが自動で判定される  
- 修正したあとに「壊れていないか」を確認するときに役立つ  

---

## unittest の基本（標準ライブラリ）

**unittest** は、Python に標準で入っているテスト用のライブラリです。

```python
# test_calc.py
import unittest              # 標準ライブラリのテストフレームワークをインポート

def add(a, b):               # テスト対象の関数
    return a + b

class TestAdd(unittest.TestCase):  # TestCase を継承したテストクラス
    def test_add_positive(self):   # 正の数同士の加算テスト
        self.assertEqual(add(2, 3), 5)  # add(2, 3) が 5 と等しいことを検証

    def test_add_zero(self):       # 0 を含む加算テスト
        self.assertEqual(add(0, 5), 5)  # add(0, 5) が 5 と等しいことを検証

if __name__ == "__main__":   # 直接実行されたときのみ
    unittest.main()          # テストを実行
```

- **TestCase** を継承したクラスに、`test_` で始まるメソッドを書く  
- **assertEqual** で「期待する値と実際の値が等しいか」を確認  

---

## 実行方法

```bash
python test_calc.py          # テストファイルを直接実行
# または
python -m unittest test_calc # unittest モジュール経由で test_calc を実行
```

---

## pytest の基本

**pytest** は、**より簡単にテストを書ける**ライブラリです。

```bash
pip install pytest           # pytest を pip でインストール
```

```python
# test_calc.py
def add(a, b):               # テスト対象の関数
    return a + b

def test_add_positive():     # test_ で始まる関数がテストとして実行される
    assert add(2, 3) == 5    # add(2, 3) が 5 であることを検証

def test_add_zero():
    assert add(0, 5) == 5    # add(0, 5) が 5 であることを検証
```

- **test_** で始まる関数がテストとして実行される  
- **assert** で「こうであるべき」を書く  

---

## 実行方法

```bash
pytest test_calc.py          # 指定ファイルのテストを実行
# または（カレントフォルダ内の test_*.py をすべて実行）
pytest                       # テストを自動検出して実行
```

---

## まとめ

- **テスト**で、関数が正しく動くか自動確認できる。
- **unittest** は標準ライブラリ。**pytest** はシンプルで人気。
- テストファイルは `test_` で始め、テスト用の関数・メソッドも `test_` で始める。
