# 第6章：エラーハンドリング

**この章の目標**  
**loader** や **action** でエラーが起きたとき、**コンポーネントの描画中**にエラーが起きたときに、ユーザーに「問題が起きました」と伝え、**再試行**や**トップへ戻る**などの導線を出したいことがあります。  
React Router v7 では、**errorElement** で「そのルート（と子）でエラーが起きたときに表示するコンポーネント」を指定できます。  
この章では、その書き方と、loader 内で **throw new Response()** する方法を、初心者向けに説明します。

---

## エラーハンドリングとは？

**エラーハンドリング**とは、**エラーが起きたときに、握りつぶさず、ユーザーに分かりやすいメッセージと次の行動（再読み込み、トップへ戻る）を提示する**ことです。

- loader で「データが取れなかった」→ 404 や 500 のメッセージを表示する。  
- コンポーネントで例外が投げられた → エラー用の画面を表示し、「再試行」ボタンを出す。

React Router では、**errorElement** に渡したコンポーネントが、そのルート（および子ルート）で発生したエラーを「受け取って表示する」役割を持ちます。

---

## errorElement とは？

**errorElement** は、**そのルート（とその子）でエラーが発生したときに、代わりに表示するコンポーネント**を指定するプロパティです。

- loader や action 内で **throw** した場合、そのルートの **errorElement** が描画される。  
- コンポーネントのレンダリング中に **throw** した場合も同様。  
- エラー用コンポーネントでは **useRouteError()** で、投げられたエラー（または Response）を取得できます。

---

## throw new Response() で 404 や 500 を返す

loader 内で「データが見つからない」「サーバーエラー」を表現するとき、**throw new Response()** を使うと、React Router がそれをエラーとして扱い、**errorElement** に渡します。

- 例：**throw new Response('Not Found', { status: 404 })** で 404 を表現する。  
- **useRouteError()** で受け取った値が **Response** のときは、**response.status** や **response.json()** で内容を確認できます。

---

## 例題：errorElement を指定する

ルート定義に **errorElement** を追加し、エラー用のコンポーネントを渡します。  
各行にコメントを付けています。

```tsx
// ファイル名の例: src/main.tsx（該当部分のみ）
import { Root } from './routes/Root';
import { RootErrorBoundary } from './routes/RootErrorBoundary';

const router = createBrowserRouter([
  {
    path: '/',
    element: <Root />,
    errorElement: <RootErrorBoundary />,
    // このルートまたは子ルートでエラーが throw されると、RootErrorBoundary が表示される
    children: [
      // ...
    ],
  },
]);
```

---

## 例題：エラー用コンポーネント（useRouteError）

**useRouteError()** で、投げられたエラー（または Response）を受け取り、メッセージと「再試行」「トップへ戻る」を表示する例です。  
各行にコメントを付けています。

```tsx
// ファイル名の例: src/routes/RootErrorBoundary.tsx
import { useRouteError, isRouteErrorResponse, useNavigate } from 'react-router-dom';
// useRouteError = この errorElement に渡された「エラーの中身」を取得する
// isRouteErrorResponse = そのエラーが Response かどうかを判定する

export function RootErrorBoundary() {
  const error = useRouteError();
  const navigate = useNavigate();

  let message = '問題が発生しました。';
  let status: number | undefined;

  if (isRouteErrorResponse(error)) {
    // loader や action で throw new Response(...) した場合、ここで true
    status = error.status;
    message = error.statusText || error.data || message;
    // Response の statusText や body をメッセージに使う（data は text のときなど）
  } else if (error instanceof Error) {
    message = error.message;
    // 通常の Error の場合は message を表示する
  }

  return (
    <div style={{ padding: '2rem', textAlign: 'center' }}>
      <h2>エラー</h2>
      {status != null && <p>ステータス: {status}</p>}
      <p>{message}</p>
      <div style={{ display: 'flex', gap: '1rem', justifyContent: 'center' }}>
        <button type="button" onClick={() => window.location.reload()}>
          再読み込み
        </button>
        <button type="button" onClick={() => navigate('/')}>
          トップへ戻る
        </button>
      </div>
    </div>
  );
}
```

---

## 例題：loader 内で 404 を throw する

「ユーザーが見つからない」ときに **throw new Response()** で 404 を伝える例です。  
各行にコメントを付けています。

```tsx
// ファイル名の例: src/routes/userDetail.tsx（loader 内）
async function userDetailLoader({ params }: { params: { id: string } }) {
  const res = await fetch(`https://api.example.com/users/${params.id}`);
  if (!res.ok) {
    throw new Response('ユーザーが見つかりません', {
      status: 404,
      statusText: 'Not Found',
    });
    // この throw により、このルートの errorElement が表示され、useRouteError() でこの Response が渡される
  }
  return res.json();
}
```

---

## まとめ

- **errorElement** で、そのルート（と子）でエラーが起きたときに表示するコンポーネントを指定する。**useRouteError()** でエラー内容を取得する。
- loader や action 内で **throw new Response('メッセージ', { status: 404 })** とすると、エラーとして errorElement に渡される。
- エラー用コンポーネントでは「再読み込み」「トップへ戻る」などの導線を必ず用意する。

次章では、**読み込み状態と Pending**（loading、useNavigation）を説明します。
