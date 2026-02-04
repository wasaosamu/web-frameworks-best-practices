# 第3章：ローダーとデータ取得

**この章の目標**  
React Router v7 では、**そのルートが表示される前にデータを取得する**処理を **loader** に書けます。  
コンポーネントでは **useLoaderData()** でそのデータを受け取り、表示に使います。  
この章では、loader の書き方と、データを「いつ・どこで」取るかの考え方を、初心者向けに説明します。

---

## ローダーとは？

**ローダー（loader）** とは、**そのルートにユーザーがアクセスしたときに、ルートのコンポーネントが描画される「前」に実行される関数**のことです。

- 例：ユーザー一覧を API から取得する、ユーザー ID に紐づく詳細を取得する  
- ローダーで取ったデータは、**useLoaderData()** でコンポーネントから参照できます。  
- 「データ取得」をルートに紐づけて書くことで、**どの URL でどんなデータが必要か**が分かりやすくなり、**遷移前にデータを読む**ことで表示の遅れを減らせます。

---

## なぜルートにローダーを書くのか？

- **データと画面をセットで考える** … 「この URL にはこのデータが必要」がルート定義にまとまる。  
- **遷移前に取得** … ユーザーがリンクをクリックした時点でローダーが動き、画面が切り替わるときにはすでにデータが用意されている（または読み込み中である）形にしやすい。  
- **コンポーネントを薄く保つ** … 「データを取る」は loader、「表示する」はコンポーネント、と役割を分けられる。

---

## loader の書き方

各ルートに **loader** プロパティを付け、**async** 関数を渡します。  
その関数の**戻り値**が、コンポーネント側で **useLoaderData()** によって受け取れます。  
loader 内で **throw** した場合は、エラーバウンダリー（第6章）や **throw** を使ったレスポンス（404 など）に委ねられます。

---

## 例題：一覧用の loader

次の例は、**ユーザー一覧**のルートに **loader** を付け、API からデータを取得して返しています。  
コンポーネントでは **useLoaderData()** でそのデータを受け取り、表示しています。  
各行にコメントを付けています。

```tsx
// ファイル名の例: src/routes/users.tsx（ルート定義とコンポーネントを同じファイルにした例）
import { useLoaderData } from 'react-router-dom';

// 型定義（API の戻り値の形を表す）
type User = {
  id: number;
  name: string;
  email: string;
};

// loader は「このルートにアクセスしたときに実行される」関数。
// 戻り値が useLoaderData() でコンポーネントに渡される
async function usersLoader(): Promise<{ users: User[] }> {
  const res = await fetch('https://api.example.com/users');
  // ユーザー一覧を取得する API を呼ぶ
  if (!res.ok) throw new Response('取得に失敗しました', { status: res.status });
  const users: User[] = await res.json();
  return { users };
  // オブジェクトで返すと、コンポーネントで useLoaderData() から { users } として受け取れる
}

export function usersRoute() {
  return {
    path: 'users',
    loader: usersLoader,
    element: <UsersPage />,
  };
}

function UsersPage() {
  const { users } = useLoaderData() as { users: User[] };
  // loader の戻り値がここに渡される。型は as で指定している（型安全は第8章で触れる）

  return (
    <div>
      <h1>ユーザー一覧</h1>
      <ul>
        {users.map((user) => (
          <li key={user.id}>
            {user.name} — {user.email}
          </li>
        ))}
      </ul>
    </div>
  );
}
```

---

## 例題：ルート定義に loader を渡す（createBrowserRouter 内）

**createBrowserRouter** の配列の中で、各ルートに **loader** を付けます。  
次の例は、**users** ルートに **usersLoader** を紐づけているイメージです。  
各行にコメントを付けています。

```tsx
// ファイル名の例: src/main.tsx（該当部分のみ）
import { createBrowserRouter, RouterProvider } from 'react-router-dom';
import { Root } from './routes/Root';
import { UsersPage, usersLoader } from './routes/users';

const router = createBrowserRouter([
  {
    path: '/',
    element: <Root />,
    children: [
      // ...
      {
        path: 'users',
        element: <UsersPage />,
        loader: usersLoader,
        // このルートにアクセスすると、まず usersLoader が実行され、
        // その戻り値が UsersPage の useLoaderData() に渡される
      },
    ],
  },
]);
```

---

## 例題：動的ルート（:id）の loader で 1 件取得

URL の **:id** を使って、**1 件だけ**データを取得する loader の例です。  
**params** から **id** を取り出し、API に渡しています。  
各行にコメントを付けています。

```tsx
// ファイル名の例: src/routes/userDetail.tsx
import { useLoaderData, useParams } from 'react-router-dom';

type User = { id: number; name: string; email: string };

type LoaderParams = { params: { id: string } };
// loader には { params } が渡される。params.id で URL の :id の値が取れる

async function userDetailLoader({ params }: LoaderParams): Promise<User> {
  const id = params.id;
  const res = await fetch(`https://api.example.com/users/${id}`);
  if (!res.ok) throw new Response('ユーザーが見つかりません', { status: 404 });
  return res.json();
}

export function UserDetailPage() {
  const user = useLoaderData() as User;

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}

// ルート定義で loader に params を渡すため、次のように書く
// loader: ({ params }) => userDetailLoader({ params })
```

---

## まとめ

- **loader** は、そのルートにアクセスしたときに**コンポーネントの描画前**に実行される関数。データ取得をここに書く。
- 取得したデータは **useLoaderData()** でコンポーネントから参照する。データと画面をルート単位でセットにできる。
- 動的セグメント（:id）は loader の引数 **params** から取り出す。1 件取得や 404 は loader 内で **throw new Response()** などで表現できる。

次章では、**アクションとフォーム**（action、Form、useActionData）を説明します。
