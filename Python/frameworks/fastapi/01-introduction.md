# FastAPI 入門

**この章の目標**  
**FastAPI** とは何か、どうやって動かすかを学びます。  
API を簡単に作れるフレームワークとして、初心者向けに説明します。

---

## FastAPI とは？

**FastAPI** は、**API を作るためのモダンな Python フレームワーク**です。

- **高速**で、Python の中でも特に速い部類  
- **型ヒント**をそのまま使って、自動でドキュメントやバリデーションが効く  
- REST API や WebSocket を簡単に作れる  

Flask より「API 特化」で、型ヒントと相性が良いのが特徴です。

---

## インストール

```bash
pip install fastapi uvicorn   # FastAPI と ASGI サーバー uvicorn をインストール
```

- **uvicorn** は、FastAPI を動かすための ASGI サーバー  

---

## 最小限のアプリ

次のコードは、FastAPI で「Hello, World!」を返す**最小限の API** です。  
各行にコメントを付けています。

```python
# ファイル名の例: main.py
from fastapi import FastAPI
# FastAPI を読み込む

app = FastAPI()
# アプリの本体を作る

@app.get("/")
# 「GET / というリクエストが来たとき」という意味

def index():
    return {"message": "Hello, World!"}
    # 辞書を返すと、自動で JSON になる

@app.get("/users/{user_id}")
def get_user(user_id: int):
    # user_id は URL から取得され、int に変換される
    return {"user_id": user_id}
```

---

## 実行方法

```bash
uvicorn main:app --reload     # main.py の app を起動、--reload でコード変更時に自動再起動
```

- **main** はファイル名、**app** は FastAPI の変数名  
- `--reload` は、コードを変えると自動で再読み込みする（開発時用）  
- ブラウザで `http://127.0.0.1:8000` を開くと、JSON が表示される  

---

## 自動ドキュメント

FastAPI は、**API のドキュメントを自動生成**します。

- `http://127.0.0.1:8000/docs` → Swagger UI（対話的なドキュメント）  
- `http://127.0.0.1:8000/redoc` → ReDoc  

型ヒントを書いておくと、ここに型や例が反映されます。

---

## まとめ

- **FastAPI** は API 特化のフレームワーク。型ヒントと相性が良い。
- **uvicorn** でサーバーを起動する。
- 辞書を返すと自動で JSON になる。
- `/docs` で API ドキュメントが確認できる。
