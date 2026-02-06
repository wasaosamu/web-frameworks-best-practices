# SQLAlchemy 入門

**この章の目標**  
**SQLAlchemy** を使って、データベースに接続する方法を学びます。  
ORM を使うと、SQL を書かずに Python で DB を操作できます。

---

## SQLAlchemy とは？

**SQLAlchemy** は、**データベースを Python から扱う**ためのライブラリです。

- **ORM**（オブジェクトとテーブルを対応付ける）で、クラスを使って DB を操作できる  
- SQLite、PostgreSQL、MySQL など複数の DB に対応  
- Flask や FastAPI と組み合わせてよく使う  

---

## インストール

```bash
pip install sqlalchemy        # SQLAlchemy を pip でインストール
```

---

## 接続の基本（SQLite の例）

```python
from sqlalchemy import create_engine

# SQLite に接続（ファイルがなければ新規作成）
engine = create_engine("sqlite:///mydb.db")

# 接続を確認
with engine.connect() as conn:
    result = conn.execute("SELECT 1")  # 簡単なクエリで接続テスト
    print(result.fetchone())           # (1,) が返れば OK
```

- **create_engine(接続文字列)** でエンジンを作成  
- **sqlite:///ファイル名** で SQLite に接続  

---

## 接続文字列の例

| DB | 接続文字列の例 |
|----|----------------|
| SQLite | `sqlite:///mydb.db` |
| PostgreSQL | `postgresql://user:pass@localhost/mydb` |
| MySQL | `mysql+pymysql://user:pass@localhost/mydb` |

- PostgreSQL や MySQL を使う場合は、別途ドライバ（psycopg2、pymysql など）のインストールが必要  

---

## まとめ

- **create_engine(接続文字列)** で DB に接続する。
- **engine.connect()** で接続を取得し、SQL を実行できる。
- SQLite なら追加のドライバ不要で使える。
