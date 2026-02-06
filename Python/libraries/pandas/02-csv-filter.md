# pandas：CSV 読み込みとフィルタ

**この章の目標**  
**CSV ファイル**の読み込みと、条件による**フィルタ**の方法を学びます。  
実務で最もよく使う pandas の使い方です。

---

## CSV を読み込む

```python
import pandas as pd

df = pd.read_csv("data.csv", encoding="utf-8")  # CSV を DataFrame に読み込む
print(df.head())               # 先頭 5 行を表示
```

- **pd.read_csv(ファイル名)** で CSV を読み込む  
- **encoding="utf-8"** で日本語にも対応  

---

## フィルタ（条件で行を絞る）

```python
# age が 25 以上の行だけ取得
adults = df[df["age"] >= 25]

# 複数条件（かつ）
result = df[(df["age"] >= 25) & (df["score"] >= 80)]

# 複数条件（または）
result = df[(df["age"] < 20) | (df["age"] > 60)]
```

- **df[条件]** で条件に合う行だけを抽出  
- **&** でかつ、**|** でまたは  

---

## ソート

```python
df_sorted = df.sort_values("score", ascending=False)  # score で降順ソート
```

- **sort_values(列名, ascending=False)** でソート  
- **ascending=False** で降順  

---

## CSV に書き出す

```python
df.to_csv("output.csv", index=False, encoding="utf-8-sig")  # インデックス列を付けずに保存
```

- **to_csv()** で DataFrame を CSV に保存  
- **index=False** で行番号を保存しない  

---

## まとめ

- **pd.read_csv()** で CSV を読み込む。
- **df[条件]** でフィルタ、**sort_values()** でソートする。
- **to_csv()** で CSV に書き出す。
