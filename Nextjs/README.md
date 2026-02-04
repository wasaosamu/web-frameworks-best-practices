# Next.js App Router + TypeScript ベストプラクティス

**対象読者**  
高校卒業したばかりで、ITに知見がない方が、技術書として読み進められることを想定しています。

**目的**  
Next.js（App Router）と TypeScript で、サーバーコンポーネントを前提にした、シンプルで保守しやすい書き方を、章ごとに学べるようにまとめました。

---

## 目次

| 章 | ファイル | 内容 |
|----|----------|------|
| **第1章** | [01-routing.md](./01-routing.md) | ルーティングと構成（page.tsx / layout.tsx、loading / error / not-found、グルーピング） |
| **第2章** | [02-server-client-components.md](./02-server-client-components.md) | Server Component と Client Component（「use client」の使い分け） |
| **第3章** | [03-data-fetching-api.md](./03-data-fetching-api.md) | データ取得とAPI（読み取りは Server の fetch、変更は Route Handler / Server Actions） |
| **第4章** | [04-cache-revalidation.md](./04-cache-revalidation.md) | キャッシュと再検証（revalidate / no-store の考え方） |
| **第5章** | [05-env-security.md](./05-env-security.md) | 環境変数とセキュリティ（.env、NEXT_PUBLIC_ の使い分け） |
| **第6章** | [06-component-design.md](./06-component-design.md) | コンポーネント設計（純粋なUI、components / features / hooks の分け方） |
| **第7章** | [07-type-safety.md](./07-type-safety.md) | 型安全性（strict、any を避ける、API の型） |
| **第8章** | [08-performance-ux.md](./08-performance-ux.md) | パフォーマンスとUX（next/image、dynamic、loading.tsx） |
| **第9章** | [09-error-handling.md](./09-error-handling.md) | エラー処理（握りつぶさない、error.tsx、ログとユーザー向けメッセージ） |
| **用語集** | [10-glossary.md](./10-glossary.md) | ベストプラクティスで出てくる単語まとめ（迷ったらここに戻る） |

---

## 読み方のすすめ

- **順番に読む**と、ルーティング → コンポーネントの種類 → データの取り方 → キャッシュ・環境変数・設計・型・パフォーマンス・エラー、と流れで理解しやすくなります。
- 各章の**例題**には、**各行にコメント**を付けているので、ソースコードを追いながら「何をしているか」を確認できます。
- 用語が分からないときは、その章の冒頭や「まとめ」を見直すと、要点が整理されています。

---

## 前提

- Next.js の **App Router**（`app/` または `src/app/` を使う構成）を前提にしています。
- **TypeScript** を使うことを想定しています。
- ベースにしている考え方は、**Server Components を基本**とし、必要な部分だけ Client にする、という方針です。

---

## フォルダ構成（章ごとのファイル）

```
nextjs-best-practices/
├── README.md                    ← このファイル（目次と読み方）
├── 01-routing.md
├── 02-server-client-components.md
├── 03-data-fetching-api.md
├── 04-cache-revalidation.md
├── 05-env-security.md
├── 06-component-design.md
├── 07-type-safety.md
├── 08-performance-ux.md
├── 09-error-handling.md
└── 10-glossary.md
```

各章のMDファイルを開いて、順に読み進めてください。
