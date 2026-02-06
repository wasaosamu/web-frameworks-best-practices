# SQLAlchemy：CRUD 操作

**この章の目標**  
**ORM** を使って、テーブルの**作成・読み取り・更新・削除**（CRUD）を行う方法を学びます。

---

## モデル（テーブル）の定義

```python
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.orm import declarative_base, Session

Base = declarative_base()     # モデルの基底クラス

class User(Base):             # テーブルに対応するクラス
    __tablename__ = "users"   # テーブル名

    id = Column(Integer, primary_key=True, autoincrement=True)
    name = Column(String(100))
    email = Column(String(255))

engine = create_engine("sqlite:///mydb.db")
Base.metadata.create_all(engine)  # テーブルを作成
```

- **Column** でカラムを定義  
- **create_all** でテーブルを DB に作成  

---

## 作成（Create）

```python
with Session(engine) as session:
    user = User(name="山田", email="yamada@example.com")
    session.add(user)         # セッションに追加
    session.commit()          # DB に反映
```

- **Session** でトランザクションを管理  
- **add()** で追加、**commit()** で確定  

---

## 読み取り（Read）

```python
with Session(engine) as session:
    users = session.query(User).all()           # 全件取得
    user = session.query(User).filter_by(id=1).first()  # ID で1件取得
```

- **query(モデル)** でクエリを組み立て  
- **filter_by()** で条件を指定  

---

## 更新（Update）

```python
with Session(engine) as session:
    user = session.query(User).filter_by(id=1).first()
    user.name = "佐藤"        # 属性を変更
    session.commit()          # 変更を反映
```

---

## 削除（Delete）

```python
with Session(engine) as session:
    user = session.query(User).filter_by(id=1).first()
    session.delete(user)      # 削除
    session.commit()
```

---

## まとめ

- **Column** でモデルを定義、**create_all** でテーブルを作成する。
- **Session** で **add**、**query**、**delete** を行い、**commit** で反映する。
- **filter_by()** で条件を指定して検索できる。
