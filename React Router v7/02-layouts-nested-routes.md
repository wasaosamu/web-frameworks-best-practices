# 第2章：レイアウトとネストルート

**この章の目標**  
多くのサイトでは、「全ページで共通のヘッダーやメニューを表示し、その中だけ画面が切り替わる」という形にします。  
React Router v7 では、**親ルートにレイアウト用のコンポーネント**を置き、**children** に子ルートを書く**ネストルート**でこれを実現します。  
この章では、その書き方を、初心者向けに説明します。

---

## レイアウトとは？

**レイアウト**とは、**複数のページで共通して使う「外枠」**のことです。

- 例：ヘッダー、ナビゲーション、フッター  
- 「どのページでも同じヘッダーを出し、その下の部分だけ URL に応じて変える」という形にすると、ユーザーが迷いにくくなります。

React Router では、**親ルートの element** に「外枠」のコンポーネントを置き、その中で **Outlet** を描画すると、「ここに子ルートの内容が差し込まれる」ようになります。

---

## ネストルートとは？

**ネストルート**とは、**ルートの入れ子**のことです。

- 親ルート：path が `/` で、レイアウト（ヘッダー + Outlet）を表示する。  
- 子ルート：path が `about` で、親が `/` なので実際の URL は `/about`。**Outlet の位置**に About の内容が表示される。

こうすると、「親のレイアウトはそのまま、子の部分だけ切り替わる」という動きになります。

---

## Outlet とは？

**Outlet** は、React Router が用意しているコンポーネントで、**「今の URL にマッチした子ルートの element」を描画する場所**です。

- 親のレイアウトコンポーネントの中で **<Outlet />** を置く。  
- ユーザーが `/about` に移動すると、**Outlet** の位置に About のコンポーネントが表示される。

「レイアウト = 共通部分 + <Outlet />」と覚えておきましょう。

---

## 例題：レイアウトと子ルートの定義

次の例は、**Root** をレイアウトとし、その **children** に Home（`/`）と About（`/about`）を置いています。  
各行にコメントを付けています。

```tsx
// ファイル名の例: src/main.tsx
import { createBrowserRouter, RouterProvider } from 'react-router-dom';
import { Root } from './routes/Root';
import { Home } from './routes/Home';
import { About } from './routes/About';

const router = createBrowserRouter([
  {
    path: '/',
    element: <Root />,
    // Root が「外枠」になる。Root の中で <Outlet /> を描画すると、子ルートの内容がそこに表示される
    children: [
      {
        index: true,
        // index: true = 親の path（/）にちょうどマッチするときのルート
        element: <Home />,
      },
      {
        path: 'about',
        // 親が / なので、/about にマッチする（path は相対なので 'about' だけでよい）
        element: <About />,
      },
    ],
  },
]);

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>,
);
```

---

## 例題：レイアウトコンポーネント（Outlet を置く）

次の例は、**Root** コンポーネントです。共通のヘッダーを表示し、**Outlet** の位置に子ルートの内容が差し込まれます。  
各行にコメントを付けています。

```tsx
// ファイル名の例: src/routes/Root.tsx
import { Outlet, Link } from 'react-router-dom';
// Outlet = 子ルートの element が描画される「穴」の場所
// Link = ページ遷移用のリンク（後述）

export function Root() {
  return (
    <div>
      <header style={{ padding: '1rem', borderBottom: '1px solid #ccc' }}>
        <nav style={{ display: 'flex', gap: '1rem' }}>
          <Link to="/">ホーム</Link>
          <Link to="/about">会社について</Link>
          {/* to で飛び先のパスを指定する。クリックするとその URL に移動し、子ルートが切り替わる */}
        </nav>
      </header>
      <main style={{ padding: '1rem' }}>
        <Outlet />
        {/* ここに、今の URL にマッチした子ルート（Home または About）の内容が表示される */}
      </main>
    </div>
  );
}
```

---

## まとめ

- **レイアウト** = 全ページで共通する外枠。親ルートの **element** にレイアウト用コンポーネントを置く。
- **ネストルート** = 親ルートの **children** に子ルートを書く。子の **path** は親に対する相対パス（例：`about` → `/about`）。
- レイアウトの中で **<Outlet />** を描画すると、その位置に子ルートのコンポーネントが表示される。

次章では、**ローダーとデータ取得**（loader、useLoaderData）を説明します。
