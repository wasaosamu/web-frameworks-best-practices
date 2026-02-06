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
from django.db import models

class User(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField()
    created_at = models.DateTimeField(auto_now_add=True)
```

- **models.Model** を継承する  
- フィールドの型で **CharField**、**EmailField**、**DateTimeField** などを使う  

---

## マイグレーション

```bash
# マイグレーションファイルを作成
python manage.py makemigrations

# データベースに適用
python manage.py migrate
```

- **makemigrations** で変更を検出し、マイグレーションファイルを作る  
- **migrate** で DB に反映する  

---

## データの操作

```python
from myapp.models import User

# 作成
user = User.objects.create(name="山田", email="yamada@example.com")

# 取得
users = User.objects.all()
user = User.objects.get(id=1)

# 更新
user.name = "佐藤"
user.save()

# 削除
user.delete()
```

---

## まとめ

- **モデル**は models.Model を継承したクラスで、DB のテーブルを表す。
- **makemigrations** と **migrate** で DB に反映する。
- **objects.create()**、**objects.all()**、**objects.get()** でデータを操作する。
