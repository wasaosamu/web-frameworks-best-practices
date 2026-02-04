# 第8章：ベストプラクティスまとめ

**この章の目標**  
React Router v7 で、読みやすく保守しやすいコードを書くための**考え方**をまとめます。  
型の扱い、loader と action の責務、エラーと読み込みの扱いなど、これまでの章を踏まえた「ベストプラクティス」を、初心者向けに整理します。

---

## データはルートに紐づける（loader / action）

- **どの URL でどんなデータが必要か**は、**loader** に書く。コンポーネントは **useLoaderData()** で受け取って表示に集中する。  
- **どの URL でどんな「送信・変更」が起きるか**は、**action** に書く。**<Form>** で送信し、必要に応じて **useActionData()** でエラー表示する。

「データ取得 = loader」「データ変更 = action」と役割を分けると、**どのルートで何が起きているか**が分かりやすくなります。

---

## 型を明示する（useLoaderData / useActionData）

**useLoaderData()** や **useActionData()** の戻り値は、TypeScript ではデフォルトで **unknown** に近い型になることがあります。  
**型を明示する**と、補完やバグの早期発見に役立ちます。

- 例：loader の戻り値の型を **type LoaderData = { users: User[] }** のように定義し、**useLoaderData() as LoaderData** でキャストする。  
- 例：action の戻り値の型を **type ActionData = { error?: string }** のように定義し、**useActionData() as ActionData | undefined** でキャストする。

loader / action の関数の戻り値に型を付けておき、その型をコンポーネント側でも共有すると、一貫性が保てます。

---

## エラーは握りつぶさず、errorElement で表示する

- loader や action で「失敗」したときは、**throw new Response()** や **throw new Error()** で例外を投げ、**errorElement** に任せる。  
- エラー用コンポーネントでは **useRouteError()** で内容を取得し、「再読み込み」「トップへ戻る」などの導線を必ず用意する。

「エラーを握りつぶさない」「ユーザーに次の行動を伝える」を心がけると、信頼性の高いアプリになります。

---

## フォーム送信は <Form> と action に寄せる

- ページ遷移を伴う送信は **<Form method="post">** とルートの **action** にまとめる。  
- 送信後は loader が再実行されるので、一覧や詳細の表示が自動で最新になる。  
- バリデーションエラーは action の**戻り値**で返し、**useActionData()** で表示する。

「フォーム = Form + action」に寄せると、データの流れが追いやすくなります。

---

## ナビゲーションは Link を優先する

- 同じアプリ内の画面遷移では、**<a href>** ではなく **<Link to>** を使う。  
- プログラムから URL を変えたいときだけ **useNavigate()** や **redirect()** を使う。

Link を使うことで、ブラウザの履歴とルーターの状態が整合し、ユーザー体験も良くなります。

---

## 例題：loader の型を共有する

loader の戻り値の型を **型定義**として切り出し、コンポーネント側でも同じ型を使う例です。  
各行にコメントを付けています。

```tsx
// ファイル名の例: src/routes/users.tsx
import { useLoaderData } from 'react-router-dom';

type User = { id: number; name: string; email: string };

export type UsersLoaderData = { users: User[] };
// loader の戻り値の型を export し、コンポーネントやテストで再利用する

async function usersLoader(): Promise<UsersLoaderData> {
  const res = await fetch('https://api.example.com/users');
  if (!res.ok) throw new Response('Failed', { status: res.status });
  const users = await res.json();
  return { users };
}

export function UsersPage() {
  const { users } = useLoaderData() as UsersLoaderData;
  // 同じ型をキャストに使うと、typo やプロパティの変更に気づきやすい
  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

---

## まとめ

- **データ取得は loader**、**データ変更は action** に紐づけ、コンポーネントは「表示」と「Form」に集中する。  
- **useLoaderData / useActionData** の型を明示し、loader / action の戻り値の型と揃える。  
- エラーは **throw** して **errorElement** で表示し、「再試行」「トップへ戻る」を用意する。  
- フォームは **<Form>** と **action** に寄せ、ナビゲーションは **<Link>** を優先する。

---

これで、React Router v7 のベストプラクティスを、章ごとの MD ファイルとして一通りまとめました。  
目次は **README.md** にあります。わからない章があれば、そこから読み返してみてください。
