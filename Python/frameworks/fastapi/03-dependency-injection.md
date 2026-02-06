# FastAPI：依存性注入（Depends）

**この章の目標**  
**Depends** を使った**依存性注入**の基本を学びます。  
共通処理（認証、DB セッションなど）を差し込む仕組みです。

---

## 依存性注入とは？

**依存性注入**は、**共通の処理を「依存」として切り出し、必要なときに渡す**仕組みです。

- 認証チェック、DB セッションの取得などを関数にまとめる  
- **Depends()** でその関数を指定すると、自動で実行されて結果が渡される  

---

## 基本的な使い方

```python
from fastapi import FastAPI, Depends   # Depends で依存性を注入

app = FastAPI()

def get_common_value():                # 依存として使う関数
    return "共通の値"

@app.get("/items")
def get_items(common: str = Depends(get_common_value)):  # get_common_value の戻り値が common に渡される
    return {"common": common}
```

- **Depends(関数)** で、その関数の戻り値を引数に渡せる  

---

## 認証の例（イメージ）

```python
from fastapi import FastAPI, Depends, HTTPException

def get_current_user(token: str = Header(None)):  # ヘッダーから token を取得
    if token != "secret":                         # トークンが不正な場合
        raise HTTPException(status_code=401, detail="認証が必要です")  # 401 を返す
    return {"user_id": 1}                         # 認証成功時はユーザー情報を返す

@app.get("/me")
def get_me(user = Depends(get_current_user)):     # このルートは get_current_user が必須
    return user                                   # 認証済みユーザー情報を返す
```

- 認証用の関数を **Depends** に渡すと、そのルートだけ認証が必要になる  

---

## まとめ

- **Depends()** で共通処理を「依存」として差し込める。
- 認証や DB セッションなど、複数のルートで使う処理に便利。
