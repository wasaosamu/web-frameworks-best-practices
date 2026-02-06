# 第17章：JSON の読み書き

**この章の目標**  
**JSON** 形式のデータを扱う方法を学びます。  
API とのやり取りや設定ファイルの読み書きを、初心者向けに説明します。

---

## JSON とは？

**JSON** は、**データをテキストで表現する形式**です。

- プログラミング言語を問わず使える  
- API のリクエスト・レスポンスによく使われる  
- Python の辞書やリストに近い形で書ける  

---

## 辞書・リストを JSON に変換する

```python
import json                        # 標準ライブラリの json をインポート

data = {
    "name": "山田",                # 辞書でデータを定義
    "age": 25,
    "hobbies": ["読書", "散歩"]    # ネスト（リスト内包）も可能
}

json_str = json.dumps(data)        # Python オブジェクトを JSON 文字列に変換
# 結果: '{"name": "山田", "age": 25, "hobbies": ["読書", "散歩"]}'
```

- **json.dumps()** で Python のオブジェクトを JSON 文字列に変換  

---

## JSON を辞書・リストに変換する

```python
json_str = '{"name": "山田", "age": 25}'
data = json.loads(json_str)

print(data["name"])  # 山田
```

- **json.loads()** で JSON 文字列を Python のオブジェクトに変換  

---

## ファイルに書き出す・読み込む

```python
# ファイルに書き出す
with open("data.json", "w", encoding="utf-8") as f:  # 書き込みモードで開く
    json.dump(data, f, ensure_ascii=False, indent=2)  # ファイルに出力（日本語そのまま、2スペースでインデント）

# ファイルから読み込む
with open("data.json", "r", encoding="utf-8") as f:  # 読み込みモードで開く
    loaded = json.load(f)                            # ファイルから JSON を読み込み辞書に変換
```

- **json.dump()** / **json.load()** はファイル用  
- **ensure_ascii=False** で日本語をそのまま出力  
- **indent=2** で見やすい形で改行  

---

## まとめ

- **json.dumps()** で Python → JSON 文字列、**json.loads()** で JSON 文字列 → Python。
- **json.dump()** / **json.load()** でファイルの書き出し・読み込みができる。
