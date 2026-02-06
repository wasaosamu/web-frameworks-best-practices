# FastAPI：リクエストボディと Pydantic

**この章の目標**  
**リクエストボディ**（POST で送る JSON）を扱う方法を学びます。  
Pydantic モデルで型を定義し、バリデーションを効かせるのが FastAPI の特徴です。

---

## リクエストボディとは？

**リクエストボディ**は、**POST や PUT で送るデータ**です。

- 通常は JSON 形式  
- FastAPI では **Pydantic** モデルで形を定義する  
- 型に合わないデータは自動でエラーになる  

---

## Pydantic モデルの定義

```python
from fastapi import FastAPI
from pydantic import BaseModel   # Pydantic の BaseModel をインポート

app = FastAPI()

class UserCreate(BaseModel):     # リクエストボディの形を定義
    name: str                    # 必須の文字列フィールド
    email: str                   # 必須の文字列フィールド
    age: int = 0                 # オプション、デフォルト 0

@app.post("/users")
def create_user(user: UserCreate):  # 型を指定すると自動でパース・バリデーションされる
    return {"message": f"{user.name} を作成しました", "email": user.email}  # user の属性にアクセス
```

- **BaseModel** を継承したクラスで、受け取るデータの形を定義する  
- 型ヒントでバリデーションがかかる  

---

## 型によるバリデーション

- `name: str` → 文字列以外はエラー  
- `age: int` → 数値以外はエラー  
- 必須フィールドが無い場合もエラー  
- `/docs` で自動生成的にフォームが作られる  

---

## まとめ

- **Pydantic の BaseModel** でリクエストボディの形を定義する。
- 関数の引数にその型を書くと、自動でパース・バリデーションされる。
- 型に合わないと 422 エラーが返る。
