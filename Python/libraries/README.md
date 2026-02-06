# Python ライブラリ

**対象読者**  
Python の基本（変数、関数、データ型）を学んだ方が、よく使うライブラリを活用できるようになることを目的としています。

**目的**  
実務でよく使う**ライブラリ**の基本を、初心者向けにまとめました。

---

## ライブラリとは？

**ライブラリ**とは、**特定の機能をまとめたコードの集まり**です。

- フレームワークと違い、「必要な機能だけ」を呼び出して使う  
- HTTP 通信、データ分析、データベース接続など、用途に応じて選ぶ  
- **pip install** でインストールし、**import** で読み込む  

---

## このフォルダの構成

| ライブラリ | フォルダ | 特徴 |
|------------|----------|------|
| **requests** | [requests/](./requests/) | HTTP 通信。API 呼び出しで必須 |
| **pytest** | [pytest/](./pytest/) | テスト。シンプルで人気のテストフレームワーク |
| **pandas** | [pandas/](./pandas/) | データ分析。表形式データの処理に強い |
| **SQLAlchemy** | [sqlalchemy/](./sqlalchemy/) | データベース ORM。SQL を書かずに DB 操作 |
| **pygame** | [pygame/](./pygame/) | ゲーム開発。2D ゲームやグラフィックアプリ向け |

---

## どれから学ぶか

| 用途 | おすすめ |
|------|----------|
| API を呼び出す | requests |
| テストを書く | pytest |
| データ分析・CSV 処理 | pandas |
| データベースを扱う | SQLAlchemy |
| ゲームを作る | pygame |

- **Web アプリ開発**なら requests と SQLAlchemy  
- **データ分析**なら pandas  
- **品質向上**なら pytest  
- **ゲーム・グラフィック**なら pygame  

---

## 各ライブラリのドキュメント一覧

### requests

| ファイル | 内容 |
|----------|------|
| [01-introduction.md](./requests/01-introduction.md) | 入門・GET リクエスト |
| [02-post-json.md](./requests/02-post-json.md) | POST と JSON の扱い |

### pytest

| ファイル | 内容 |
|----------|------|
| [01-introduction.md](./pytest/01-introduction.md) | 入門・基本的なテスト |
| [02-fixtures.md](./pytest/02-fixtures.md) | フィクスチャ（前処理の共通化） |

### pandas

| ファイル | 内容 |
|----------|------|
| [01-introduction.md](./pandas/01-introduction.md) | 入門・DataFrame の基本 |
| [02-csv-filter.md](./pandas/02-csv-filter.md) | CSV 読み込みとフィルタ |

### SQLAlchemy

| ファイル | 内容 |
|----------|------|
| [01-introduction.md](./sqlalchemy/01-introduction.md) | 入門・接続と基本 |
| [02-crud.md](./sqlalchemy/02-crud.md) | CRUD 操作（作成・読み取り・更新・削除） |

### pygame

| ファイル | 内容 |
|----------|------|
| [01-introduction.md](./pygame/01-introduction.md) | 入門・ウィンドウとゲームループ |
| [02-events-drawing.md](./pygame/02-events-drawing.md) | イベントと描画（キー入力・図形） |

---

## 前提

- **Python** の基本（変数、関数、データ型）が分かっていると理解しやすいです。
- 各ライブラリのドキュメントを、順番に読み進めてください。
