# 第4章：アクションとフォーム

**この章の目標**  
React Router v7 では、**フォームの送信**や**データの変更**（登録・更新・削除）を **action** に紐づけて書けます。  
**<Form>** を使うと、送信先の URL とメソッドがルーターと連動し、送信後に **loader の再実行**でデータを最新にできます。  
この章では、action の書き方と Form の使い方を、初心者向けに説明します。

---

## アクションとは？

**アクション（action）** とは、**そのルートに対して「データを送る」リクエスト（主にフォーム送信）が来たときに実行される関数**のことです。

- 例：お問い合わせフォームの送信、ユーザー登録、記事の更新  
- アクションが終わると、React Router は**そのルート（および関連するルート）の loader を再実行**し、表示データを最新にします。  
- 「データの変更」をルートに紐づけて書くことで、**どの URL でどんな処理が走るか**が分かりやすくなります。

---

## <Form> コンポーネントとは？

React Router の **<Form>** は、**通常の <form> のように見えるが、送信を JavaScript で処理し、ページ全体の再読み込みを防ぐ**コンポーネントです。

- **action** 属性で「送信先の URL（ルート）」を指定する。  
- **method** で **post** を指定すると、その URL に紐づいた **action** 関数が呼ばれる。  
- 送信後、ルーターが loader を再実行するので、一覧や詳細の表示が自動で更新されます。

「フォーム送信 = そのルートの action を呼ぶ」と考えるとわかりやすいです。

---

## useActionData とは？

**useActionData()** は、**直前に実行された action の戻り値**をコンポーネントから参照するためのフックです。

- 例：action が「バリデーションエラー」を返したとき、その内容を useActionData() で受け取り、フォームの近くにエラーメッセージを表示する。  
- 成功時は **redirect()** で別の URL に飛ばし、useActionData は使わない、という形もよくあります。

---

## 例題：お問い合わせフォームと action

次の例は、**/contact** に **action** を定義し、**<Form>** で送信するとその action が実行される形です。  
action 内で **request.formData()** からフォームの値を取り、バリデーションや API 送信を行っています。  
各行にコメントを付けています。

```tsx
// ファイル名の例: src/routes/contact.tsx
import { Form, useActionData } from 'react-router-dom';
import type { ActionFunctionArgs } from 'react-router-dom';

// action の戻り値の型（エラー時）
type ActionData = {
  error?: string;
  success?: boolean;
};

// action は「このルートに Form で post されたとき」に実行される
async function contactAction({
  request,
}: ActionFunctionArgs): Promise<ActionData | Response> {
  const formData = await request.formData();
  // Form で送信されたデータを FormData として受け取る

  const name = formData.get('name') as string | null;
  const email = formData.get('email') as string | null;
  const message = formData.get('message') as string | null;
  // get('name') で、input の name="name" の値を取得する

  if (!name?.trim() || !email?.trim() || !message?.trim()) {
    return { error: 'すべての項目を入力してください' };
    // バリデーションに失敗したら、エラーオブジェクトを返す。useActionData() で受け取れる
  }

  // ここで API に送信するなど、実際の処理を行う
  // await fetch('/api/contact', { method: 'POST', body: formData });

  return { success: true };
  // 成功時は success を返す。または redirect('/thanks') で別ページへ飛ばしてもよい
}

export function ContactPage() {
  const actionData = useActionData() as ActionData | undefined;
  // 直前に実行された action の戻り値。初回表示や GET のときは undefined

  return (
    <div>
      <h1>お問い合わせ</h1>
      {actionData?.error && (
        <p style={{ color: 'red' }}>{actionData.error}</p>
      )}
      {actionData?.success && (
        <p style={{ color: 'green' }}>送信しました。</p>
      )}
      <Form method="post">
        {/* method="post" にすると、このルートの action が呼ばれる */}
        <div>
          <label htmlFor="name">お名前</label>
          <input id="name" name="name" type="text" required />
          {/* name 属性の値が formData.get('name') で取れる */}
        </div>
        <div>
          <label htmlFor="email">メールアドレス</label>
          <input id="email" name="email" type="email" required />
        </div>
        <div>
          <label htmlFor="message">メッセージ</label>
          <textarea id="message" name="message" required />
        </div>
        <button type="submit">送信</button>
      </Form>
    </div>
  );
}

// ルート定義で action を渡す
// action: contactAction
```

---

## 例題：ルート定義に action を渡す

**createBrowserRouter** の該当ルートに **action** を付けます。  
各行にコメントを付けています。

```tsx
// ファイル名の例: src/main.tsx（該当部分のみ）
import { ContactPage, contactAction } from './routes/contact';

const router = createBrowserRouter([
  {
    path: '/',
    element: <Root />,
    children: [
      // ...
      {
        path: 'contact',
        element: <ContactPage />,
        action: contactAction,
        // Form で method="post" かつ action がこのルート（/contact）のとき、contactAction が実行される
      },
    ],
  },
]);
```

---

## まとめ

- **action** は、そのルートに「データを送る」リクエスト（主に Form の post）が来たときに実行される関数。**request.formData()** で送信内容を受け取る。
- **<Form method="post">** を使うと、送信先ルートの action が呼ばれ、送信後に loader が再実行されて表示が最新になる。
- バリデーションエラーなどは action の**戻り値**で返し、コンポーネントで **useActionData()** を受け取って表示する。

次章では、**ナビゲーション**（Link、useNavigate、useSearchParams）を説明します。
