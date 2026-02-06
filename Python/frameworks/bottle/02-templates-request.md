# Bottle：テンプレートとリクエスト

**この章の目標**  
Bottle で**テンプレート**と**リクエスト**を扱う方法を学びます。  
Flask に近い書き方で、簡単な Web アプリを作る基本です。

---

## テンプレートの使い方

```python
from bottle import route, run, template

@route("/hello/<name>")
def hello(name):
    return template("hello", name=name)
```

- **template("ファイル名", 変数=値)** でテンプレートを描画する  
- デフォルトでは `./views/` フォルダを探す（設定で変更可能）  

---

## テンプレートファイル

```
myapp/
├── app.py
└── views/
    └── hello.tpl
```

```html
<!-- views/hello.tpl -->
<!DOCTYPE html>
<html>
<body>
  <h1>こんにちは、{{name}} さん</h1>
</body>
</html>
```

- **{{変数名}}** で変数を表示する  

---

## リクエストの取得

```python
from bottle import route, run, request

@route("/search")
def search():
    keyword = request.query.get("keyword", "")
    return f"検索: {keyword}"

@route("/login", method="POST")
def login():
    username = request.forms.get("username", "")
    return f"ログイン: {username}"
```

| 取得方法 | 用途 |
|----------|------|
| **request.query.get("名前")** | GET パラメータ |
| **request.forms.get("名前")** | POST フォーム |
| **request.json** | JSON ボディ |

---

## まとめ

- **template()** でテンプレートを描画する。`views/` に .tpl を置く。
- **request.query**、**request.forms** でリクエストを取得する。
- Flask と似た感覚で使える。
