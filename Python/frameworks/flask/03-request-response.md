# Flask：リクエストとレスポンス

**この章の目標**  
**リクエスト**（送られてきたデータ）と**レスポンス**（返すデータ）の扱い方を学びます。  
フォーム送信や JSON API で必要になる基本です。

---

## リクエストの取得

```python
from flask import Flask, request   # request でリクエスト情報を取得

app = Flask(__name__)

@app.route("/search")
def search():
    keyword = request.args.get("keyword", "")  # GET パラメータ（?keyword=〇〇）を取得、無ければ ""
    return f"検索: {keyword}"

@app.route("/login", methods=["POST"])  # POST のみ受け付ける
def login():
    username = request.form.get("username", "")  # POST フォームの username を取得
    return f"ログイン: {username}"
```

| 取得方法 | 用途 |
|----------|------|
| **request.args.get("名前")** | GET のクエリパラメータ |
| **request.form.get("名前")** | POST のフォームデータ |
| **request.json** | JSON ボディ |

---

## レスポンスの返し方

```python
from flask import Flask, jsonify, redirect, url_for

# 通常の HTML / テキスト
return "Hello"
return render_template("index.html")

# JSON を返す
return jsonify({"message": "OK", "count": 10})

# リダイレクト
return redirect(url_for("index"))
return redirect("/")
```

- **jsonify()** で JSON レスポンスを返す（Content-Type が自動で設定される）  

---

## HTTP メソッドの指定

```python
@app.route("/users", methods=["GET"])   # GET リクエストのみ受け付ける
def get_users():
    return jsonify([])                  # 空のリストを JSON で返す

@app.route("/users", methods=["POST"])  # POST リクエストのみ受け付ける
def create_user():
    return jsonify({"id": 1})           # 作成結果を JSON で返す
```

- **methods=["GET", "POST"]** で受け付けるメソッドを指定する  
- 指定しないと GET のみ  

---

## まとめ

- **request.args**（GET）、**request.form**（POST）、**request.json**（JSON）でリクエストを取得する。
- **jsonify()** で JSON レスポンスを返す。
- **methods** で HTTP メソッドを指定する。
