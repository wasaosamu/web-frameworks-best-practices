# 第1章：ルーティングの基本

**この章の目標**  
React Router v7 では、**どの URL でどの画面（コンポーネント）を表示するか**を、**ルート**という単位で定義します。  
この章では、**createBrowserRouter** と **RouterProvider** を使った、基本的なルーティングの書き方を、初心者向けに説明します。

---

## ルーティングとは？

**ルーティング**とは、「この URL（アドレス）を開いたら、この画面を表示する」という**対応づけ**のことです。

- 例：`/` を開く → トップページを表示  
- 例：`/about` を開く → 「会社について」のページを表示  
- 例：`/users/123` を開く → ユーザー ID が 123 のページを表示  

React だけでは「どの URL でどのコンポーネントを出すか」は自分で書く必要があります。  
**React Router** を使うと、**URL とコンポーネントの対応**を宣言的に書けるようになります。

---

## v7 で使う createBrowserRouter と RouterProvider

React Router v7 では、**createBrowserRouter** で「ルートの一覧」を定義し、**RouterProvider** に渡してアプリ全体にルーターを効かせます。

- **createBrowserRouter** … 「どの path に、どの Component を表示するか」を配列で書く。  
- **RouterProvider** … そのルート定義を受け取り、今の URL に応じて該当するコンポーネントを表示する。

昔の **BrowserRouter** + **Routes / Route** の書き方ではなく、**オブジェクトでルートを定義する**形が v7 の基本です。

---

## path と Component

各ルートは、少なくとも次の2つを持ちます。

- **path** … このルートが担当する URL のパス（例：`/`、`/about`、`/users/:id`）。  
- **Component** … そのパスにアクセスしたときに表示する**React コンポーネント**。

`:id` のように **コロン + 名前** を付けると、「ここが URL の一部として変わる」という**動的セグメント**になります。  
例：`/users/:id` なら、`/users/1` や `/users/2` にマッチし、コンポーネント側で **useParams()** から `id` を取得できます。

---

## 例題：最小限のルーター定義

次の例は、**トップ**（`/`）と **About**（`/about`）の2つのルートを定義し、**main.tsx**（または **index.tsx**）で **RouterProvider** に渡しているイメージです。  
各行にコメントを付けています。

```tsx
// ファイル名の例: src/main.tsx（または src/index.tsx）
import React from 'react';
import ReactDOM from 'react-dom/client';
import { createBrowserRouter, RouterProvider } from 'react-router-dom';
// createBrowserRouter = ルートの定義を配列で受け取り、ルーターを作る
// RouterProvider = そのルーターをアプリに適用するコンポーネント

import { Root } from './routes/Root';
import { Home } from './routes/Home';
import { About } from './routes/About';
// 各ルートで表示するコンポーネントを読み込む

const router = createBrowserRouter([
  // ルートの配列。上から順に「どの path にどの Component を出すか」を定義する
  {
    path: '/',
    // トップページのパス
    element: <Root />,
    // ここでは Root を「レイアウト」として使い、子ルートを children で書く形もできる（第2章）
    children: [
      {
        index: true,
        // index: true は「親の path そのもの」にマッチする（ここでは /）
        element: <Home />,
      },
      {
        path: 'about',
        // 親が / なので、/about にマッチする
        element: <About />,
      },
    ],
  },
]);

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <RouterProvider router={router} />
    {/* 上で作った router を渡すと、URL に応じて該当するコンポーネントが表示される */}
  </React.StrictMode>,
);
```

---

## 例題：シンプルにルートだけ並べる形

「レイアウトなしで、ルートを並べるだけ」の場合は、次のように **path** と **element** だけを書きます。  
各行にコメントを付けています。

```tsx
// ファイル名の例: src/main.tsx
import { createBrowserRouter, RouterProvider } from 'react-router-dom';
import { Home } from './routes/Home';
import { About } from './routes/About';

const router = createBrowserRouter([
  {
    path: '/',
    element: <Home />,
    // / にアクセスしたら Home を表示する
  },
  {
    path: '/about',
    element: <About />,
    // /about にアクセスしたら About を表示する
  },
]);

function App() {
  return <RouterProvider router={router} />;
}
```

---

## 例題：動的セグメント（:id）を使う

URL の一部を「変数」にしたいときは、**path** に **:id** のように書きます。  
コンポーネント側では **useParams()** でその値を取得します。  
各行にコメントを付けています。

```tsx
// ルート定義の例
{
  path: '/users/:id',
  element: <UserDetail />,
  // /users/1 や /users/2 にアクセスすると UserDetail が表示され、
  // useParams() で id が "1" や "2" として取れる
}
```

```tsx
// ファイル名の例: src/routes/UserDetail.tsx
import { useParams } from 'react-router-dom';
// useParams = 現在の URL の「動的セグメント」をオブジェクトで取得するフック

export function UserDetail() {
  const { id } = useParams<{ id: string }>();
  // URL が /users/123 なら、id は "123" になる

  return (
    <div>
      <h1>ユーザー詳細</h1>
      <p>ユーザー ID: {id}</p>
    </div>
  );
}
```

---

## まとめ

- **ルーティング** = 「この URL → この画面」の対応。React Router で **path** と **element**（表示するコンポーネント）を定義する。
- v7 では **createBrowserRouter** でルートの配列を定義し、**RouterProvider** に渡してアプリに適用する。
- URL の一部を変数にするときは **path** に **:id** のように書き、コンポーネントでは **useParams()** で取得する。

次章では、**レイアウトとネストルート**（共通の外枠と、その中の子ルート）を説明します。
