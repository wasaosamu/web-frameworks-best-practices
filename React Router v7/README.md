# React Router v7 + TypeScript ベストプラクティス

**対象読者**  
高校卒業したばかりで、ITに知見がない方が、技術書として読み進められることを想定しています。

**目的**  
React Router v7 と TypeScript で、**データローダー**や**アクション**を活かした、読みやすく保守しやすい**フロントエンドのルーティング**の書き方を、章ごとに学べるようにまとめました。

---

## React Router とは？

**React Router** は、**React** で作った Web アプリで「どの URL でどの画面を表示するか」を決めるためのライブラリです。  
**v7** では、**loader**（データ取得）と **action**（フォーム送信・データ変更）をルートに紐づけて書けるようになり、**データと画面**を一体で扱いやすくなっています。

---

## 目次

| 章 | ファイル | 内容 |
|----|----------|------|
| **第1章** | [01-routing-basics.md](./01-routing-basics.md) | ルーティングの基本（createBrowserRouter、Route、RouterProvider） |
| **第2章** | [02-layouts-nested-routes.md](./02-layouts-nested-routes.md) | レイアウトとネストルート（共通の外枠、子ルート） |
| **第3章** | [03-loaders-data-loading.md](./03-loaders-data-loading.md) | ローダーとデータ取得（loader、useLoaderData） |
| **第4章** | [04-actions-forms.md](./04-actions-forms.md) | アクションとフォーム（action、Form、useActionData） |
| **第5章** | [05-navigation.md](./05-navigation.md) | ナビゲーション（Link、useNavigate、useSearchParams） |
| **第6章** | [06-error-handling.md](./06-error-handling.md) | エラーハンドリング（ErrorBoundary、errorElement） |
| **第7章** | [07-loading-pending.md](./07-loading-pending.md) | 読み込み状態と Pending（loading、useNavigation） |
| **第8章** | [08-best-practices.md](./08-best-practices.md) | ベストプラクティスまとめ（型安全、責務の分離） |
| **用語集** | [09-glossary.md](./09-glossary.md) | ベストプラクティスで出てくる単語まとめ（迷ったらここ） |

---

## 読み方のすすめ

- **順番に読む**と、ルーティングの基本 → レイアウト → データ取得（loader）→ フォーム（action）→ ナビゲーション → エラー・読み込み → まとめ、と流れで理解しやすくなります。
- 各章の**例題**には、**各行にコメント**を付けているので、ソースコードを追いながら「何をしているか」を確認できます。
- 用語が分からないときは、その章の冒頭や「まとめ」を見直すと、要点が整理されています。

---

## 前提

- **React** と **React Router v7** を使う構成を前提にしています。
- **TypeScript** を使うことを想定しています。
- フロントエンド（ブラウザで動く SPA）のルーティングが主な対象です。

---

## フォルダ構成（章ごとのファイル）

```
React Router v7/
├── README.md                    ← このファイル（目次と読み方）
├── 01-routing-basics.md
├── 02-layouts-nested-routes.md
├── 03-loaders-data-loading.md
├── 04-actions-forms.md
├── 05-navigation.md
├── 06-error-handling.md
├── 07-loading-pending.md
├── 08-best-practices.md
└── 09-glossary.md
```

各章の MD ファイルを開いて、順に読み進めてください。
