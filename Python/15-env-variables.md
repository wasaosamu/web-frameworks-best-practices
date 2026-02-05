# 第15章：環境変数

**この章の目標**  
**環境変数**を使って、API キーやパスワードなどの**機密情報**を安全に扱う方法を学びます。  
初心者向けに、基本的な使い方を説明します。

---

## 環境変数とは？

**環境変数**は、**プログラムの外側で設定する値**です。

- API キー、データベースのパスワードなどを**ソースコードに直接書かない**  
- 開発用・本番用で値を切り替えやすい  
- .env ファイルに書いて、Git には含めない  

---

## 環境変数の読み方（os.environ）

```python
import os

# 環境変数を読む
api_key = os.environ.get("API_KEY")

if api_key is None:
    raise ValueError("API_KEY が設定されていません")
```

- **os.environ.get("名前")** で環境変数を取得。なければ **None**  
- **os.environ["名前"]** だと、未設定のとき KeyError になる  

---

## python-dotenv の使い方

**.env** ファイルに変数を書いて、プログラムから読み込む方法です。

```bash
pip install python-dotenv
```

```python
# .env ファイルの例:
# API_KEY=sk_xxxxxxxx
# DATABASE_URL=postgresql://localhost/mydb
```

```python
from dotenv import load_dotenv
import os

load_dotenv()  # .env を読み込む

api_key = os.environ.get("API_KEY")
```

- **load_dotenv()** で、プロジェクトのルートにある .env を読み込む  

---

## .env を Git に含めない

**.gitignore** に次を追加します。

```
.env
.env.local
```

- 機密情報がリポジトリに含まれないようにする  

---

## .env.example で必要な変数を共有

実際の値は書かず、**変数名だけ**を書いた .env.example をリポジトリに含めます。

```
# .env.example
API_KEY=
DATABASE_URL=
```

- 他の開発者が「どの変数が必要か」を把握しやすくなる  

---

## まとめ

- **環境変数**で機密情報をソースコード外に置く。
- **os.environ.get()** で読み取る。
- **python-dotenv** で .env から読み込む。.env は .gitignore に追加する。
