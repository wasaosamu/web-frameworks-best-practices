# pandas 入門

**この章の目標**  
**pandas** を使って、表形式のデータを扱う方法を学びます。  
データ分析や CSV 処理でよく使うライブラリです。

---

## pandas とは？

**pandas** は、**表形式のデータ**（テーブル）を扱うライブラリです。

- **DataFrame** という表形式のデータ構造が中心  
- CSV の読み込み、フィルタ、集計が簡単にできる  
- データ分析・機械学習の前処理でよく使う  

---

## インストール

```bash
pip install pandas            # pandas を pip でインストール
```

---

## DataFrame の基本

```python
import pandas as pd           # 慣例で pd と略してインポート

# 辞書から DataFrame を作る
data = {
    "name": ["山田", "佐藤", "鈴木"],
    "age": [25, 30, 22],
    "score": [80, 90, 75]
}
df = pd.DataFrame(data)       # 辞書から DataFrame を作成

print(df)                     # 表形式で表示
```

- **pd.DataFrame(辞書)** で DataFrame を作成  
- 辞書のキーが列名、値が列のデータになる  

---

## 列・行へのアクセス

```python
print(df["name"])             # 列を取得（Series になる）
print(df[["name", "age"]])    # 複数列を取得

print(df.iloc[0])             # 0 行目を取得
print(df.loc[0, "name"])      # 0 行目の "name" 列の値
```

- **df["列名"]** で列を取得  
- **iloc** でインデックス指定、**loc** で行・列を指定  

---

## 基本的な集計

```python
print(df["age"].mean())       # 平均
print(df["score"].sum())      # 合計
print(df.describe())          # 基本統計量（平均、標準偏差など）を一括表示
```

---

## まとめ

- **pd.DataFrame()** で表形式のデータを作成する。
- **df["列名"]** で列にアクセス、**iloc** / **loc** で行・列を指定する。
- **mean()**、**sum()**、**describe()** で集計できる。
