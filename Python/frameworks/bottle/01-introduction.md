# Bottle 入門

**この章の目標**  
**Bottle** とは何か、どうやって動かすかを学びます。  
シンプルで軽量なフレームワークとして、初心者向けに説明します。

---

## Bottle とは？

**Bottle** は、**1ファイルで完結する軽量な Web フレームワーク**です。

- **依存が少なく**、Bottle 単体で動く  
- Flask に似た書き方で、**学習コストが低い**  
- 小規模なアプリやプロトタイプに向いている  

「とりあえず簡単に Web アプリを作りたい」ときに適しています。

---

## インストール

```bash
pip install bottle   # Bottle を pip でインストール
```

---

## 最小限のアプリ

次のコードは、Bottle で「Hello, World!」を表示する**最小限のアプリ**です。

```python
# ファイル名の例: app.py
from bottle import route, run

@route("/")
def index():
    return "Hello, World!"

run(host="localhost", port=8080)
```

- **@route("/")** で URL と関数を紐付ける  
- **run()** でサーバーを起動する  

---

## 実行方法

```bash
python app.py              # アプリを直接実行してサーバー起動
```

- ブラウザで `http://localhost:8080` を開くと、「Hello, World!」が表示される  

---

## Flask との違い

| 項目 | Bottle | Flask |
|------|--------|-------|
| サイズ | 1ファイルで完結 | もう少し機能が多い |
| 用途 | 小規模・プロトタイプ | 小〜中規模 |
| 書き方 | Flask に似ている | 拡張が豊富 |

---

## まとめ

- **Bottle** は軽量でシンプルな Web フレームワーク。
- 1ファイルで完結し、学習しやすい。
- 小規模なアプリやプロトタイプに向いている。
