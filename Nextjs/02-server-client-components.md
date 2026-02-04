# 第2章：Server Component と Client Component

**この章の目標**  
Next.jsでは、画面の部品（コンポーネント）が「**サーバーで動くもの**」と「**ブラウザで動くもの**」の2種類に分かれます。  
この違いを理解し、「どちらを使うか」を正しく選べるようになることが目標です。

---

## 2種類のコンポーネント

Next.jsのコンポーネントは、次の2つに分けて考えます。

| 種類 | どこで動く？ | 主な用途 |
|------|--------------|----------|
| **Server Component** | サーバー（会社のPCやクラウド上にある「サイトを組み立てる機械」） | データ取得、SEOに有利な静的な表示 |
| **Client Component** | ユーザーのブラウザ（Chrome や Safari など） | ボタンクリック、入力フォーム、アニメーションなど「操作に反応する」部分 |

イメージとしては、

- **Server** = 料理を作る厨房。ここでデータを集めて「完成したHTML」を用意する。
- **Client** = お客さんの席。ここで「ボタンを押した」「文字を入力した」といった**操作**に反応する。

最初は「**Server Component が基本**」で、**本当にブラウザで動かしたい部分だけ** Client にします。

---

## なぜ「サーバー」と「クライアント」を分けるのか？

- **サーバーでやること**  
  データベースや外部APIからデータを取る、秘密のキーを使ってサービスに問い合わせる、など。  
  これらはユーザーのブラウザには見せず、サーバーだけで行う方が安全で、SEOにも有利です。

- **ブラウザでやること**  
  クリックやキー入力に反応する、ウィンドウのサイズに合わせて表示を変える、など。  
  これらはユーザーの操作に合わせて**その場で**動く必要があるので、Client Component で書きます。

「データ取りや秘密の処理はサーバー」「操作や見た目の反応はクライアント」と役割を分けると、安全で速いサイトになります。

---

## 既定は Server Component

Next.jsでは、**特別な指定をしなければ、そのコンポーネントは Server Component** です。

- ファイルの**先頭に何も書かない**、または `"use client"` を**書かない** → Server Component  
- **ブラウザ専用の機能**（後述）を使うときだけ、ファイルの先頭に `"use client"` を書いて Client Component にする

つまり、「まずは Server で考えて、必要な部分だけ Client にする」が基本です。

---

## Client Component にすべき場合

次のようなときは、**そのコンポーネント（またはその部分だけを抜き出したコンポーネント）** を Client にします。

1. **ブラウザのAPIを使うとき**  
   例：`window`、`localStorage`、`document` など。これらはサーバーには存在しないので、Client でしか使えません。

2. **イベントハンドラを使うとき**  
   例：`onClick`（クリックしたとき）、`onChange`（入力が変わったとき）など。ユーザーの操作に反応する部分は Client で処理します。

3. **状態（state）を持たせたいとき**  
   例：「今、この入力欄には何が入っているか」「このタブはどれが開いているか」など。React の `useState` を使う場合は Client が必要です。

4. **カスタムフック（use〇〇）を使うとき**  
   多くのフックは内部で state やブラウザAPIを使うため、そのコンポーネントは Client にします。

逆に言うと、

- データを取って表示するだけ
- ボタンもフォームもなく、ただテキストや画像を並べるだけ

のような部分は、**Server Component のまま**で問題ありません。重いデータ取得やSEOが重要なページは、できるだけ Server で完結させるとよいです。

---

## 「use client」の書き方

Client Component にしたいファイルの**一番上**に、次の1行を書きます。

```tsx
"use client";
// ↑ このファイルの中では、ブラウザ用の機能（useState, onClick など）が使える、という宣言です

import { useState } from "react";
// useState は「状態」を持たせるための React の機能で、Client でしか使えません

export default function Counter() {
  const [count, setCount] = useState(0);
  // count が「今の数字」、setCount が「その数字を更新する関数」です

  return (
    <div>
      <p>クリック回数: {count}</p>
      {/* ボタンを押すと setCount が呼ばれ、count が 1 増えます */}
      <button onClick={() => setCount(count + 1)}>+1</button>
    </div>
  );
}
```

- `"use client"` は**文字列**として、**ファイルの先頭**に書く必要があります。  
- この1行があるファイルで定義したコンポーネントは、すべて **Client Component** として扱われます。  
- 必要なら、**Client にしたい部分だけ**を別ファイルに分けて、そのファイルにだけ `"use client"` を付ける、という設計にすると、Server と Client の役割が分かりやすくなります。

---

## 例題：Server と Client を組み合わせる

- ページ全体は Server Component のまま（データ取得やSEOをサーバーで行う）
- 「カウンター」のように「クリックで数字が変わる」部分だけ、別ファイルの Client Component にする

### 例題1：Client Component（ボタンで増減するカウンター）

```tsx
// ファイル名の例: app/components/Counter.tsx
"use client";
// このファイルは Client Component なので、useState や onClick が使えます

import { useState } from "react";
// useState = 「状態」を扱うための React の機能（ブラウザ側で動く）

export default function Counter() {
  const [count, setCount] = useState(0);
  // count: 今表示する数字（初期値 0）
  // setCount: count を更新する関数

  return (
    <div style={{ padding: "1rem", border: "1px solid #ccc" }}>
      <p>現在の値: {count}</p>
      {/* クリックすると count が 1 増える */}
      <button onClick={() => setCount(count + 1)}>+1</button>
      {/* クリックすると count が 1 減る */}
      <button onClick={() => setCount(count - 1)}>-1</button>
    </div>
  );
}
```

### 例題2：Server Component のページ（ここでデータ取得もできる）

```tsx
// ファイル名の例: app/page.tsx
// このファイルには "use client" がない → Server Component です
// ここでは onClick や useState は使わず、「表示する内容」を組み立てます

import Counter from "./components/Counter";
// Counter は Client Component なので、ページの中で「ここだけブラウザで動く」として埋め込まれます

export default function HomePage() {
  return (
    <div>
      <h1>トップページ</h1>
      <p>以下はブラウザで動くカウンターです。</p>
      {/* Server で組み立てたページの中に、Client の Counter を配置 */}
      <Counter />
    </div>
  );
}
```

このように、「ページ全体は Server」「操作が必要な部分だけ Client」と分けると、安全で効率の良い構成になります。

---

## まとめ

- Next.js のコンポーネントは **Server Component**（サーバーで動く）と **Client Component**（ブラウザで動く）の2種類。
- **原則は Server**。ブラウザAPI・イベント・状態・フックが必要な部分だけ **Client**（ファイル先頭に `"use client"`）。
- データ取得やSEOが重要なページは、できるだけ Server Component で完結させる。
- Client にしたい部分は、別ファイルに切り出してそのファイルにだけ `"use client"` を付けると整理しやすい。

次章では、**データの取得の仕方**と**APIの役割**を、初心者向けに説明します。
