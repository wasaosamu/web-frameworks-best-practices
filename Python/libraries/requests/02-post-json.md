# requests：POST と JSON

**この章の目標**  
**POST** リクエストと **JSON** の扱い方を学びます。  
API にデータを送る際によく使うパターンです。

---

## POST リクエスト

```python
import requests

# フォームデータを送る場合
data = {"name": "山田", "email": "yamada@example.com"}  # フォーム形式の辞書
response = requests.post("https://api.example.com/users", data=data)  # data= でフォーム送信

print(response.status_code)    # 201 Created など
```

- **requests.post(URL, data=辞書)** でフォーム形式のデータを送る  

---

## JSON を送る・受け取る

```python
import requests

# JSON で送る
payload = {"name": "山田", "age": 25}
response = requests.post(
    "https://api.example.com/users",
    json=payload,              # json= で自動で JSON に変換され、Content-Type も設定される
)

# レスポンスの JSON を取得
result = response.json()       # 辞書として取得
print(result["id"])            # 作成された ID など
```

- **json=** に辞書を渡すと、自動で JSON に変換され、ヘッダーも設定される  
- **response.json()** でレスポンスの JSON を辞書に変換  

---

## ヘッダーを指定する

```python
headers = {"Authorization": "Bearer token123", "Content-Type": "application/json"}  # ヘッダー辞書
response = requests.get("https://api.example.com/me", headers=headers)  # headers= でヘッダーを指定
```

- **headers=** でリクエストヘッダーを指定する  
- API キーやトークンの付与によく使う  

---

## エラーハンドリング

```python
try:
    response = requests.get("https://api.example.com/users", timeout=5)  # timeout で秒数指定
    response.raise_for_status()   # 4xx, 5xx の場合に例外を発生させる
    data = response.json()
except requests.exceptions.RequestException as e:  # ネットワークエラーやタイムアウトなど
    print("エラー:", e)
```

- **raise_for_status()** でエラー時に例外を発生させる  
- **timeout** でタイムアウト秒数を指定できる  

---

## まとめ

- **requests.post(URL, json=辞書)** で JSON を送る。
- **response.json()** でレスポンスの JSON を辞書に変換する。
- **headers=** でヘッダーを指定、**raise_for_status()** でエラーを検出する。
