# Django：モデル（データベース）

**この章の目標**  
**モデル**を使って、データベースのテーブルを Python のクラスで定義する方法を学びます。  
Django の中心的な機能のひとつです。

---

## モデルとは？

**モデル**は、**データベースのテーブルをクラスで表したもの**です。

- models.py でクラスを定義すると、テーブルが作られる  
- マイグレーションで DB に反映する  
- ORM で SQL を書かずにデータを扱える  

---

## モデルの定義

```python
# myapp/models.py
from django.db import models         # Django のモデル基底クラスをインポート

class User(models.Model):            # models.Model を継承してテーブルを定義
    name = models.CharField(max_length=100)       # 文字列フィールド（最大100文字）
    email = models.EmailField()                   # メール形式のフィールド
    created_at = models.DateTimeField(auto_now_add=True)  # 作成日時（自動設定）
```

- **models.Model** を継承する  
- フィールドの型で **CharField**、**EmailField**、**DateTimeField** などを使う  

---

## マイグレーション

```bash
# マイグレーションファイルを作成
python manage.py makemigrations   # モデル変更を検出し、マイグレーションファイルを生成

# データベースに適用
python manage.py migrate          # マイグレーションを DB に反映
```

- **makemigrations** で変更を検出し、マイグレーションファイルを作る  
- **migrate** で DB に反映する  

---

## データの操作

```python
from myapp.models import User

# 作成
user = User.objects.create(name="山田", email="yamada@example.com")  # 新規レコードを作成して保存

# 取得
users = User.objects.all()        # 全件取得（QuerySet を返す）
user = User.objects.get(id=1)     # ID で1件取得（見つからないと DoesNotExist）

# 更新
user.name = "佐藤"                # 属性を変更
user.save()                       # 変更を DB に保存

# 削除
user.delete()                     # レコードを削除
```

---

## まとめ

- **モデル**は models.Model を継承したクラスで、DB のテーブルを表す。
- **makemigrations** と **migrate** で DB に反映する。
- **objects.create()**、**objects.all()**、**objects.get()** でデータを操作する。
