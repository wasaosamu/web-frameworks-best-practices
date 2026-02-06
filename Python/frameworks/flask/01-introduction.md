# Flask 入門

**この章の目標**  
**Flask** とは何か、どうやって動かすかを学びます。  
最小限のアプリを作り、ルーティングの基本を、初心者向けに説明します。

---

## Flask とは？

**Flask** は、Python で Web アプリを作るための**軽量なフレームワーク**です。

- **シンプル**で、覚えることが少ない  
- 小規模なアプリや **REST API** に向いている  
- 「必要最小限」から始めて、徐々に機能を足していける  

---

## インストール

Flask を使うには、まずインストールします。

```bash
pip install flask          # Flask を pip でインストール
```

- **pip** は、Python のパッケージをインストールするツール  
- ターミナル（コマンドライン）で実行する  

---

## 最小限のアプリ

次のコードは、Flask で「Hello, World!」を表示する**最小限のアプリ**です。  
各行にコメントを付けています。

```python
# ファイル名の例: app.py
from flask import Flask    # Flask クラスをインポート

app = Flask(__name__)      # アプリ本体を作成（__name__ でモジュール名を渡す）

@app.route("/")            # ルート URL "/" にアクセスされたときの処理を紐付け
def index():
    return "Hello, World!" # 返した文字列がレスポンスとして表示される

if __name__ == "__main__": # 直接実行されたときのみ（import 時は実行しない）
    app.run(debug=True)    # 開発サーバーを起動（debug=True で自動リロード有効）
```

---

## 各部の説明

### `@app.route("/")`

| 部分 | 意味 |
|------|------|
| **@app.route** | 「この URL でこの関数を呼ぶ」と紐付けるための**デコレータ** |
| **"/"** | ルート（トップページ）の URL。例：`http://localhost:5000/` |

### `def index():`

- **index** は関数名。任意の名前でよい  
- この関数の**戻り値**が、ブラウザに表示される  

### `app.run(debug=True)`

- **app.run()** で、開発用サーバーを起動する  
- **debug=True** は、コードを変更すると自動で再読み込みする設定（開発時のみ使う）  

---

## 複数のルートを追加する

```python
@app.route("/")
def index():
    return "Hello, World!"

@app.route("/about")
def about():
    return "このサイトについて"

@app.route("/users/<name>")
def user(name):
    return f"こんにちは、{name} さん"
```

| URL | 表示される内容 |
|-----|----------------|
| `/` | Hello, World! |
| `/about` | このサイトについて |
| `/users/山田` | こんにちは、山田 さん |

- **`<name>`** は「URL の一部が変わる」ことを表す。関数の引数 `name` に渡される  

---

## 実行方法

```bash
python app.py              # アプリを起動（Flask 開発サーバーが立ち上がる）
```

- ブラウザで `http://127.0.0.1:5000/` を開くと、アプリが表示される  
- 終了するときは、ターミナルで **Ctrl + C**  

---

## まとめ

- **Flask** は軽量な Web フレームワーク。`pip install flask` でインストールする。
- **@app.route("/")** で「この URL でこの関数を呼ぶ」と紐付ける。
- 関数の**戻り値**が、ブラウザに表示される。
- **`<name>`** のように書くと、URL の一部を変数として受け取れる。
