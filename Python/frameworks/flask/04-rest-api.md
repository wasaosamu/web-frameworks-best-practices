# Flask：REST API の作り方

**この章の目標**  
Flask で **REST API**（JSON でデータをやり取りする API）の基本を学びます。  
GET / POST / エラーハンドリングの考え方を、初心者向けに説明します。

---

## REST API とは？

**REST API** は、**HTTP で JSON をやり取りする API** の一種です。

- フロントエンドやモバイルアプリから呼ばれる  
- GET（取得）、POST（作成）、PUT/PATCH（更新）、DELETE（削除）を使い分ける  

---

## 簡単な CRUD 風の例

```python
from flask import Flask, request, jsonify

app = Flask(__name__)
users = [{"id": 1, "name": "山田"}]  # メモリ上のユーザー一覧（本来は DB）

@app.route("/users", methods=["GET"])
def get_users():
    return jsonify(users)              # 全ユーザーを JSON で返す

@app.route("/users", methods=["POST"])
def create_user():
    data = request.get_json()          # リクエストボディの JSON を辞書に変換
    name = data.get("name", "")        # name を取得、無ければ空文字
    new_id = max(u["id"] for u in users) + 1  # 既存 ID の最大値 + 1 を新 ID に
    users.append({"id": new_id, "name": name})  # ユーザーを追加
    return jsonify({"id": new_id, "name": name}), 201  # 201 Created で作成結果を返す
```

- **request.get_json()** で JSON ボディを取得  
- タプル `(レスポンス, ステータスコード)` でステータスを指定できる  

---

## エラーレスポンス

```python
@app.route("/users/<int:user_id>")     # URL の数値を user_id として受け取る
def get_user(user_id):
    user = next((u for u in users if u["id"] == user_id), None)  # ID でユーザーを検索
    if user is None:                   # 見つからなかった場合
        return jsonify({"error": "Not found"}), 404  # 404 Not Found を返す
    return jsonify(user)               # 見つかったユーザーを JSON で返す
```

- **404** は Not Found、**400** は Bad Request、**500** はサーバーエラー  

---

## まとめ

- **jsonify()** で JSON を返し、**request.get_json()** で JSON を受け取る。
- **methods** で GET / POST などを使い分ける。
- エラー時は適切なステータスコード（404 など）を返す。
