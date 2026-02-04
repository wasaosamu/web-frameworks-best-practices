# 第5章：ナビゲーション

**この章の目標**  
ユーザーが**別のページへ移動する**とき、**リンクをクリックする**方法と、**プログラムから URL を変える**方法があります。  
React Router v7 では、**Link**、**useNavigate**、**useSearchParams** などで、ページ遷移やクエリパラメータを扱います。  
この章では、それらの使い方を、初心者向けに説明します。

---

## リンクで遷移する：<Link>

**<Link>** は、**クリックすると指定した URL に移動し、ページ全体を再読み込みせずにルートだけ切り替える**コンポーネントです。

- **to** 属性で「飛び先のパス」を指定する。  
- 通常の **<a href="...">** だと、クリックするとページ全体が再読み込みされます。**<Link to="...">** を使うと、React Router が**クライアント側で**ルートだけ切り替えるため、速くスムーズに感じられます。

「画面遷移には **Link** を使う」と覚えておきましょう。

---

## プログラムから遷移する：useNavigate

**useNavigate()** は、**コンポーネントの中から「この URL に移動する」という処理を実行する**ためのフックです。

- 例：フォーム送信成功後に「ありがとうページ」へ飛ばす、ボタンクリックで「戻る」を行う。  
- **navigate('/path')** で指定したパスへ移動する。  
- **navigate(-1)** でブラウザの「戻る」と同じ動きをさせられます。

「リンクではなく、処理の結果で URL を変えたいとき」に useNavigate を使います。

---

## クエリパラメータ：useSearchParams

**useSearchParams()** は、**URL のクエリ文字列（?key=value）を読み書きする**ためのフックです。

- 例：`/users?page=2&sort=name` の **page** や **sort** を読み取り、一覧の表示に反映する。  
- **searchParams.get('page')** で値を取得し、**setSearchParams({ page: '2' })** でクエリを変更（結果として URL が変わり、画面が更新される）できます。

---

## 例題：Link で一覧から詳細へ

一覧の各行をクリックすると、その ID の詳細ページへ飛ぶ例です。  
各行にコメントを付けています。

```tsx
// ファイル名の例: src/routes/users.tsx（該当部分のみ）
import { Link, useLoaderData } from 'react-router-dom';

function UsersPage() {
  const { users } = useLoaderData() as { users: User[] };

  return (
    <div>
      <h1>ユーザー一覧</h1>
      <ul>
        {users.map((user) => (
          <li key={user.id}>
            <Link to={`/users/${user.id}`}>
              {/* to に動的なパスを指定。クリックすると /users/1 などに移動する */}
              {user.name}
            </Link>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

---

## 例題：useNavigate で送信後にリダイレクト

action 内で**送信成功時に別のページへ飛ばす**例です。  
**redirect()** を使うと、action の戻り値として「この URL にリダイレクトする」と伝えられます。  
各行にコメントを付けています。

```tsx
// ファイル名の例: src/routes/contact.tsx（action 内の該当部分のみ）
import { redirect } from 'react-router-dom';

async function contactAction({ request }: ActionFunctionArgs) {
  const formData = await request.formData();
  // ... バリデーションや API 送信 ...

  // 成功したら「ありがとう」ページへ飛ばす
  return redirect('/thanks');
  // redirect を返すと、React Router がその URL にナビゲートする
}
```

コンポーネント内で「ボタンを押したら戻る」を行う例です。

```tsx
import { useNavigate } from 'react-router-dom';

function DetailPage() {
  const navigate = useNavigate();
  // navigate 関数を取得する

  return (
    <div>
      <button type="button" onClick={() => navigate(-1)}>
        戻る
      </button>
      {/* navigate(-1) でブラウザの「戻る」と同じ動き */}
      <button type="button" onClick={() => navigate('/')}>
        トップへ
      </button>
      {/* navigate('/') でトップページへ移動 */}
    </div>
  );
}
```

---

## 例題：useSearchParams でページネーション

URL の **?page=2** を読み取り、一覧の表示に反映する例です。  
各行にコメントを付けています。

```tsx
// ファイル名の例: src/routes/users.tsx（該当部分のみ）
import { useSearchParams, Link } from 'react-router-dom';

function UsersPage() {
  const [searchParams, setSearchParams] = useSearchParams();
  // searchParams = 現在のクエリを表す URLSearchParams
  // setSearchParams = クエリを変更し、URL を更新する（再レンダーされる）

  const page = Number(searchParams.get('page')) || 1;
  // ?page=2 なら page は 2。未指定なら 1

  return (
    <div>
      <h1>ユーザー一覧</h1>
      {/* 一覧の表示は page を使って API や loader に渡す */}
      <div>
        <Link to={`?page=${page - 1}`}>前へ</Link>
        <span>ページ {page}</span>
        <Link to={`?page=${page + 1}`}>次へ</Link>
        {/* Link の to にクエリ付きのパスを指定すると、?page=... が変わる */}
      </div>
    </div>
  );
}
```

---

## まとめ

- **<Link to="パス">** で、クリック時にそのパスへ遷移する。ページ全体の再読み込みは発生しない。
- **useNavigate()** で、処理の結果として URL を変えたり、**navigate(-1)** で戻る動きをさせたりする。**redirect()** は action 内で「成功時に別ページへ飛ばす」ときに使う。
- **useSearchParams()** で、URL のクエリ（?key=value）を読み書きする。一覧のページネーションや絞り込みに使える。

次章では、**エラーハンドリング**（ErrorBoundary、errorElement）を説明します。
