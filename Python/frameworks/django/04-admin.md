# Django：管理画面（admin）

**この章の目標**  
**管理画面（admin）** の基本的な使い方を学びます。  
モデルを登録するだけで、データの一覧・追加・編集ができる画面が用意されます。

---

## 管理画面とは？

**管理画面**は、**データベースの内容をブラウザで操作する画面**です。

- モデルを登録するだけで使える  
- 一覧・追加・編集・削除が最初から使える  
- ブログの記事管理や社内ツールによく使う  

---

## 管理者ユーザーの作成

```bash
python manage.py createsuperuser    # 管理画面用の管理者ユーザーを作成
```

- ユーザー名、メール、パスワードを入力する  
- 作成後、`/admin/` にアクセスしてログインする  

---

## モデルを管理画面に登録

```python
# myapp/admin.py
from django.contrib import admin
from .models import User

@admin.register(User)               # User モデルを管理画面に登録
class UserAdmin(admin.ModelAdmin):  # 表示のカスタマイズ用クラス
    list_display = ["name", "email", "created_at"]  # 一覧に表示する列を指定
    search_fields = ["name", "email"]               # 検索対象のフィールドを指定
```

- **@admin.register(モデル)** でモデルを登録する  
- **list_display** で一覧に表示する列を指定  
- **search_fields** で検索対象のフィールドを指定  

---

## アクセス方法

- サーバー起動後、`http://127.0.0.1:8000/admin/` にアクセス  
- createsuperuser で作ったユーザーでログインする  

---

## まとめ

- **createsuperuser** で管理者を作成する。
- **admin.py** でモデルを登録し、**list_display** などで表示をカスタマイズする。
- `/admin/` で管理画面にアクセスする。
