# requests 入門

**この章の目標**  
**requests** を使って、HTTP リクエスト（API 呼び出しなど）を行う方法を学びます。  
実務で最もよく使うライブラリのひとつです。

---

## requests とは？

**requests** は、**HTTP 通信**を行うためのライブラリです。

- Python 標準の `urllib` よりシンプルに書ける  
- API の呼び出し、Web スクレイピング、ファイルのダウンロードなどで使う  
- 実務でほぼ必須のライブラリ  

---

## インストール

```bash
pip install requests          # requests を pip でインストール
```

---

## GET リクエストの基本

```python
import requests               # requests をインポート

response = requests.get("https://api.example.com/users")  # GET で URL にアクセス

print(response.status_code)   # HTTP ステータスコード（200 など）を取得
print(response.text)          # レスポンスの本文を文字列で取得
```

- **requests.get(URL)** で GET リクエストを送る  
- 戻り値の **response** にステータスコードや本文が入る  

---

## ステータスコードの確認

```python
response = requests.get("https://api.example.com/users")

if response.ok:                # 200〜299 なら True
    data = response.json()     # JSON 形式なら辞書に変換して取得
    print(data)
else:
    print("エラー:", response.status_code)  # 404, 500 などを表示
```

- **response.ok** で成功（2xx）かどうかを簡単に判定  
- **response.json()** で JSON を辞書に変換  

---

## クエリパラメータを付ける

```python
params = {"keyword": "Python", "limit": 10}  # クエリパラメータの辞書
response = requests.get("https://api.example.com/search", params=params)
# 実際の URL: https://api.example.com/search?keyword=Python&limit=10
```

- **params** に辞書を渡すと、自動でクエリ文字列が付く  

---

## まとめ

- **requests.get(URL)** で GET リクエストを送る。
- **response.status_code**、**response.text**、**response.json()** でレスポンスを取得する。
- **params** でクエリパラメータを指定できる。
