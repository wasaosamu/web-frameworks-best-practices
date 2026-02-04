# 第7章：読み込み状態と Pending

**この章の目標**  
ユーザーが**リンクをクリックして別のルートに移動するとき**や、**フォームを送信したとき**、データの取得や処理が終わるまで少し時間がかかることがあります。  
そのあいだ「読み込み中です」と分かる表示（スピナーやスケルトン）を出したい場合、React Router v7 では **loading** や **useNavigation** を使います。  
この章では、その考え方と書き方を、初心者向けに説明します。

---

## 読み込み状態とは？

**読み込み状態**とは、**「今、データを取っている最中」「フォームを送信した処理が終わるのを待っている最中」** といった「処理が完了していない」状態のことです。

- 例：リンクをクリックしたが、次のページの loader がまだ終わっていない → 「読み込み中」と表示する。  
- 例：送信ボタンを押したが、action がまだ終わっていない → ボタンを「送信中…」にしたり、フォームを無効にしたりする。

ユーザーに「今、何かが動いている」と伝えることで、待ち時間のストレスを減らせます。

---

## loading（ルートの loading プロパティ）

React Router v7 では、ルートに **loading** プロパティを付けると、**そのルートの loader が完了するまで**、指定したコンポーネントが表示されます。

- 親ルートに **loading** を付けると、**子ルートへの遷移時**に、子の loader が終わるまでその loading が表示される、という動きになる場合があります。  
- 「このルートに遷移したとき、loader が終わるまでこの UI を出したい」というときに **loading** でコンポーネントを指定します。

（注：v7 の「loading」の正確な仕様は、公式ドキュメントの **defer / pending** とあわせて確認するとよいです。ここでは「loader 完了前の表示」のイメージで説明します。）

---

## useNavigation とは？

**useNavigation()** は、**今、ルーターが「ナビゲーション中」か「フォーム送信中」か** を教えてくれるフックです。

- **navigation.state** が **'loading'** のときは、別のルートへの遷移（loader 実行中）などで「読み込み中」と判断できる。  
- **navigation.state** が **'submitting'** のときは、Form の送信（action 実行中）と判断できる。  
- これを使って、「送信中はボタンを無効にする」「読み込み中はスピナーを出す」といった UI を書けます。

---

## 例題：ルートに loading を指定する

親ルートに **loading** を付け、子ルートへの遷移時に「読み込み中」の表示を出す例です。  
各行にコメントを付けています。

```tsx
// ファイル名の例: src/main.tsx（該当部分のみ）
const router = createBrowserRouter([
  {
    path: '/',
    element: <Root />,
    loadingElement: <LoadingSpinner />,
    // 子ルートへの遷移時など、loader が完了するまで LoadingSpinner を表示する（v7 の仕様に合わせて loadingElement の場合あり）
    children: [
      // ...
    ],
  },
]);
```

```tsx
// ファイル名の例: src/components/LoadingSpinner.tsx
export function LoadingSpinner() {
  return (
    <div style={{ padding: '2rem', textAlign: 'center' }}>
      <p>読み込み中...</p>
    </div>
  );
}
```

（注：v7 では **loadingElement** や **HydrateFallback** など、モードによって名前が異なる場合があります。公式ドキュメントで確認してください。）

---

## 例題：useNavigation で送信中のボタンを無効にする

**useNavigation().state === 'submitting'** のとき、送信ボタンを無効にし、「送信中…」と表示する例です。  
各行にコメントを付けています。

```tsx
// ファイル名の例: src/routes/contact.tsx（該当部分のみ）
import { Form, useActionData, useNavigation } from 'react-router-dom';

export function ContactPage() {
  const actionData = useActionData() as ActionData | undefined;
  const navigation = useNavigation();
  // navigation.state で「idle」「loading」「submitting」のいずれかが取れる

  const isSubmitting = navigation.state === 'submitting';
  // action が実行中（フォーム送信中）のとき true

  return (
    <div>
      <Form method="post">
        {/* ... 入力欄 ... */}
        <button type="submit" disabled={isSubmitting}>
          {isSubmitting ? '送信中...' : '送信'}
        </button>
        {/* 送信中はボタンを無効にし、ラベルを「送信中...」に変える */}
      </Form>
    </div>
  );
}
```

---

## 例題：useNavigation で遷移中の表示をする

「リンクをクリックしたが、次のページの loader がまだ終わっていない」ときに、ヘッダーなどに「読み込み中」を表示する例です。  
各行にコメントを付けています。

```tsx
// ファイル名の例: src/routes/Root.tsx（該当部分のみ）
import { Outlet, Link, useNavigation } from 'react-router-dom';

export function Root() {
  const navigation = useNavigation();
  const isLoading = navigation.state === 'loading';
  // 別のルートへの遷移中（loader 実行中）のとき true

  return (
    <div>
      <header>
        <nav>
          <Link to="/">ホーム</Link>
          <Link to="/about">会社について</Link>
        </nav>
        {isLoading && (
          <div style={{ fontSize: '0.875rem', color: '#666' }}>
            読み込み中...
          </div>
        )}
      </header>
      <main>
        <Outlet />
      </main>
    </div>
  );
}
```

---

## まとめ

- **読み込み状態**をユーザーに伝えると、待ち時間のストレスを減らせる。ルートの **loading**（または **loadingElement**）で「loader 完了前」の表示を指定できる。
- **useNavigation()** で **state** が **'loading'**（遷移中）か **'submitting'**（フォーム送信中）かを判定し、ボタンの無効化や「送信中…」「読み込み中…」の表示に使う。
- 送信ボタンは **disabled={navigation.state === 'submitting'}** にすると、二重送信を防ぎやすい。

次章では、**ベストプラクティスまとめ**（型安全、責務の分離）を説明します。
