# Django 入門

**この章の目標**  
**Django** とは何か、プロジェクトの作り方を学びます。  
プロジェクト構成と、最初のページ表示までを、初心者向けに説明します。

---

## Django とは？

**Django** は、Python で Web アプリを作るための**フル機能のフレームワーク**です。

- **管理画面**（admin）が最初から付いている  
- **データベース**との連携がしやすい  
- ブログ、EC サイト、社内システムなど、中〜大規模なアプリに向いている  

Flask より「最初に揃っているものが多い」分、覚えることは多くなります。

---

## インストール

Django を使うには、まずインストールします。

```bash
pip install django          # Django を pip でインストール
```

---

## プロジェクトの作成

**プロジェクト**は、アプリ全体の「箱」のようなものです。

```bash
django-admin startproject myproject   # プロジェクトのフォルダとファイルを生成
cd myproject                          # プロジェクトフォルダに移動
```

- **myproject** の部分は、好きな名前でよい  
- このコマンドで、プロジェクト用のフォルダとファイルが作られる  

---

## フォルダ構成（作成直後）

```
myproject/
├── manage.py          ← コマンドを実行する入口
├── myproject/         ← プロジェクト設定のフォルダ
│   ├── __init__.py
│   ├── settings.py    ← 設定（DB、言語など）
│   ├── urls.py        ← URL と処理の対応
│   └── wsgi.py
```

- **manage.py** … サーバー起動やマイグレーションなど、Django のコマンドを実行する  
- **settings.py** … データベースや言語など、プロジェクトの設定を書く  
- **urls.py** … 「この URL のとき、この処理を実行する」と対応付ける  

---

## アプリの作成

Django では、**プロジェクト**の中に**アプリ**を複数作る形になります。

```bash
python manage.py startapp myapp
```

- **myapp** の部分は、機能に合わせた名前（例：`users`、`products`）にする  

---

## 最初のページを表示する

### 1. ビュー（処理）を書く

`myapp/views.py` に、ページの処理を書きます。

```python
# myapp/views.py
from django.http import HttpResponse   # レスポンス用クラスをインポート

def index(request):                    # ビュー関数（request にリクエスト情報が入る）
    return HttpResponse("Hello, World!")  # 返した文字列がレスポンスとして返される
```

- **request** は、ブラウザから送られたリクエストの情報  
- **HttpResponse** で、返す内容をラップする  

### 2. URL を設定する

`myproject/urls.py` で、URL とビューを紐付けます。

```python
# myproject/urls.py
from django.contrib import admin
from django.urls import path
from myapp import views

urlpatterns = [
    path("admin/", admin.site.urls),   # 管理画面の URL
    path("", views.index),             # ルート "/" にアクセスしたら views.index を実行
]
```

### 3. アプリを登録する

`myproject/settings.py` の `INSTALLED_APPS` に、作ったアプリを追加します。

```python
INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    # ...
    "myapp",  # ここに追加
]
```

---

## サーバーを起動する

```bash
python manage.py runserver    # 開発サーバーを起動（デフォルト 8000 番ポート）
```

- ブラウザで `http://127.0.0.1:8000/` を開くと、「Hello, World!」が表示される  
- 終了するときは、ターミナルで **Ctrl + C**  

---

## まとめ

- **Django** はフル機能の Web フレームワーク。`pip install django` でインストールする。
- **プロジェクト** = アプリ全体の箱。**アプリ** = 機能ごとのまとまり。
- **views.py** で処理を書き、**urls.py** で URL と紐付ける。
- **python manage.py runserver** で開発サーバーを起動する。
