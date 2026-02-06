# Python フレームワーク

**対象読者**  
Python の基本（変数、関数、データ型）を学んだ方が、フレームワークを使って Web アプリを作れるようになることを目的としています。

**目的**  
Python で Web アプリを作るための代表的なフレームワーク（**Flask** と **Django**）の基本を、初心者向けにまとめました。

---

## フレームワークとは？

**フレームワーク**とは、**アプリを作るときの土台**のようなものです。

- よく使う機能（ルーティング、データベース接続など）が最初から用意されている  
- 決まった書き方に従うと、効率よく開発できる  
- 「車輪の再発明」をせず、本題の開発に集中できる  

---

## このフォルダの構成

| フレームワーク | フォルダ | 特徴 |
|----------------|----------|------|
| **Flask** | [flask/](./flask/) | 軽量でシンプル。小規模なアプリに向く |
| **Django** | [django/](./django/) | 機能が豊富。中〜大規模なアプリに向く |
| **FastAPI** | [fastapi/](./fastapi/) | API 特化。型ヒントと相性が良い |
| **Bottle** | [bottle/](./bottle/) | 1ファイルで完結。最小限の構成 |

---

## どれを選ぶか

| 選ぶ目安 | Flask | Django | FastAPI | Bottle |
|----------|-------|--------|---------|--------|
| 規模 | 小規模 | 中〜大規模 | API 中心 | 最小限 |
| 学習のしやすさ | 入りやすい | 覚えることが多い | 型ヒントが分かると楽 | とても簡単 |
| 用途 | Web アプリ、API | ブログ、EC、管理システム | REST API、WebSocket | プロトタイプ、簡単な Web |

- **Web アプリ**なら Flask または Django  
- **API をメイン**に作るなら FastAPI  
- **最小構成で試したい**なら Bottle  

まずは **Flask** で基本を学び、用途に合わせて他のフレームワークを選ぶとよいでしょう。

---

---

## 各フレームワークのドキュメント一覧

### Flask

| ファイル | 内容 |
|----------|------|
| [01-introduction.md](./flask/01-introduction.md) | 入門・基本構成 |
| [02-templates.md](./flask/02-templates.md) | Jinja2 テンプレート |
| [03-request-response.md](./flask/03-request-response.md) | リクエスト・レスポンス |
| [04-rest-api.md](./flask/04-rest-api.md) | REST API の作り方 |

### Django

| ファイル | 内容 |
|----------|------|
| [01-introduction.md](./django/01-introduction.md) | 入門・基本構成 |
| [02-models.md](./django/02-models.md) | モデル（データベース） |
| [03-templates.md](./django/03-templates.md) | テンプレート |
| [04-admin.md](./django/04-admin.md) | 管理画面（admin） |

### FastAPI

| ファイル | 内容 |
|----------|------|
| [01-introduction.md](./fastapi/01-introduction.md) | 入門・基本構成 |
| [02-request-body.md](./fastapi/02-request-body.md) | リクエストボディと Pydantic |
| [03-dependency-injection.md](./fastapi/03-dependency-injection.md) | 依存性注入（Depends） |

### Bottle

| ファイル | 内容 |
|----------|------|
| [01-introduction.md](./bottle/01-introduction.md) | 入門・基本構成 |
| [02-templates-request.md](./bottle/02-templates-request.md) | テンプレートとリクエスト |

---

## 前提

- **Python** の基本（変数、関数、データ型）が分かっていると理解しやすいです。
- 各フレームワークのドキュメントを、順番に読み進めてください。
