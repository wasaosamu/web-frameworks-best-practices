# NestJS + TypeScript ベストプラクティス

**対象読者**  
高校卒業したばかりで、ITに知見がない方が、技術書として読み進められることを想定しています。

**目的**  
NestJS と TypeScript で、スケーラブルで保守しやすい**バックエンドAPI**を書くための考え方を、章ごとに学べるようにまとめました。

---

## NestJS とは？

NestJS は、**Node.js** 上で動く**サーバーサイド**（バックエンド）のフレームワークです。  
「API の窓口（URL）を用意する」「データベースとやり取りする」「認証する」といった処理を、**役割ごとに分けて**書けるようにするための土台です。

---

## 目次

| 章 | ファイル | 内容 |
|----|----------|------|
| **第1章** | [01-project-structure.md](./01-project-structure.md) | プロジェクト構成とモジュール（フォルダの分け方、モジュールの役割） |
| **第2章** | [02-controllers-routing.md](./02-controllers-routing.md) | コントローラーとルーティング（URL と処理の対応づけ） |
| **第3章** | [03-services-di.md](./03-services-di.md) | サービスと依存性注入（ビジネスロジックの置き場所、DI） |
| **第4章** | [04-dto-validation.md](./04-dto-validation.md) | DTO とバリデーション（リクエストの形の定義とチェック） |
| **第5章** | [05-database-repository.md](./05-database-repository.md) | データベースとリポジトリ（データの永続化、責務の分離） |
| **第6章** | [06-auth-authorization.md](./06-auth-authorization.md) | 認証・認可（ログイン、誰が何をしていいか） |
| **第7章** | [07-error-handling.md](./07-error-handling.md) | エラーハンドリングと例外フィルター（エラーの統一的な扱い方） |
| **第8章** | [08-middleware-guards.md](./08-middleware-guards.md) | ミドルウェアとガード（リクエスト前の共通処理、アクセス制御） |
| **第9章** | [09-env-config.md](./09-env-config.md) | 環境変数と設定（開発・本番の切り替え、秘密情報の管理） |
| **用語集** | [10-glossary.md](./10-glossary.md) | ベストプラクティスで出てくる単語まとめ（処理順のざっくり図も） |

---

## 読み方のすすめ

- **順番に読む**と、構成 → ルーティング → サービス → DTO・DB・認証・エラー・ミドルウェア・設定、と流れで理解しやすくなります。
- 各章の**例題**には、**各行にコメント**を付けているので、ソースコードを追いながら「何をしているか」を確認できます。
- 用語が分からないときは、その章の冒頭や「まとめ」を見直すと、要点が整理されています。

---

## 前提

- **NestJS** の基本的なセットアップ（`nest new` でプロジェクト作成済み）を前提にしています。
- **TypeScript** を使うことを想定しています。
- バックエンド（API サーバー）の設計が主な対象です。

---

## フォルダ構成（章ごとのファイル）

```
NestJS/
├── README.md                    ← このファイル（目次と読み方）
├── 01-project-structure.md
├── 02-controllers-routing.md
├── 03-services-di.md
├── 04-dto-validation.md
├── 05-database-repository.md
├── 06-auth-authorization.md
├── 07-error-handling.md
├── 08-middleware-guards.md
├── 09-env-config.md
└── 10-glossary.md
```

各章の MD ファイルを開いて、順に読み進めてください。
