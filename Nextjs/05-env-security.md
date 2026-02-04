# 第5章：環境変数とセキュリティ

**この章の目標**  
「APIキー」「データベースのパスワード」など、**外部に漏らしてはいけない情報**を、Next.jsでは**環境変数**として扱います。  
この章では、環境変数の置き場所と、**どの情報をブラウザに出してよいか・出してはいけないか**を、初心者向けに説明します。

---

## 環境変数とは？

**環境変数**とは、プログラムが動く「環境」に持たせておく**設定値**のことです。

- 例：「データベースに接続するときのパスワード」「外部APIを使うときの秘密のキー」  
- これらを**ソースコードに直接書かない**ようにし、  
  「この名前の環境変数に、本番用の値が入っている」という形で読み出すようにします。

そうすることで、

- ソースコードをGitに上げても、**秘密の値は含まれない**  
- 開発用・本番用で**違う値**を簡単に切り替えられる  

というメリットがあります。

Next.jsでは、プロジェクトのルートに **.env** というファイルを置き、その中に「変数名=値」を書いて環境変数を定義します。

---

## 絶対に守りたいルール：秘密は「サーバーだけ」で使う

- **機密情報**（APIキー、シークレット、パスワード、秘密鍵など）は、**.env** に置き、**サーバー側のコード**（Server Component、Route Handler、Server Actions、データアクセス層など）からだけ読み出します。
- **NEXT_PUBLIC_** が付いた環境変数は、Next.jsが**ブラウザに送る**ため、誰でも開発者ツールなどで見ることができます。  
  → だから **NEXT_PUBLIC_** には、**公開してよい値だけ**を入れます。秘密のキーやパスワードは絶対に付けません。

まとめると、

| 種類 | どこで使う？ | ブラウザに見える？ | 例 |
|------|--------------|---------------------|-----|
| 秘密の値（**NEXT_PUBLIC_ を付けない**） | サーバー側のみ | **見えない** | APIキー、DBパスワード |
| 公開してよい値（**NEXT_PUBLIC_** を付ける） | サーバー・クライアント両方 | **見える** | 公開用のAPIのベースURLなど |

---

## .env の書き方

Next.jsでは、プロジェクトの**ルート**（package.json がある場所）に、次のようなファイルを置きます。

- **.env** … 共通で使う環境変数（通常、Gitには含めず .gitignore に書いておく）
- **.env.local** … 自分のPCだけの設定（開発用のキーなど。これもGitに含めない）
- **.env.example** … 「どんな変数名が必要か」の見本だけを書いたファイル（値は書かず、Gitに含めてOK）

### 例：.env（実際の値は本番用・開発用で別ファイルや別環境で設定）

```bash
# データベースの接続情報（秘密なので NEXT_PUBLIC_ は付けない）
DATABASE_URL=postgresql://user:password@localhost:5432/mydb

# 外部APIの秘密キー（サーバー側でしか使わない）
API_SECRET_KEY=sk_xxxxxxxxxxxxxxxx

# ブラウザからも見てよい「公開用」の設定だけ NEXT_PUBLIC_ を付ける
NEXT_PUBLIC_APP_NAME=マイアプリ
```

- `DATABASE_URL` や `API_SECRET_KEY` は、**サーバー側のコード**から `process.env.DATABASE_URL` のように参照します。ブラウザには送られません。
- `NEXT_PUBLIC_APP_NAME` は、クライアント側でも `process.env.NEXT_PUBLIC_APP_NAME` で参照でき、**ブラウザに含まれる**ので、公開してよい値だけを入れます。

---

## 例題：サーバー側でだけ秘密のキーを使う

次の例は、**Server Component** の中で、秘密の環境変数を使ってAPIを呼び出すイメージです。  
このコードはサーバーでしか実行されないので、`API_SECRET_KEY` はブラウザに送られません。  
各行にコメントを付けています。

```tsx
// ファイル名の例: app/admin/page.tsx
// "use client" がないので Server Component。この中で process.env を読んでもブラウザには出ません

export default async function AdminPage() {
  const apiKey = process.env.API_SECRET_KEY;
  // 環境変数 API_SECRET_KEY の値を読みます（.env に API_SECRET_KEY=xxx と書いてある想定）
  // この値はサーバーでしか存在せず、HTMLとしてブラウザに送られることはありません

  if (!apiKey) {
    throw new Error("API_SECRET_KEY が設定されていません");
    // 本番では、環境変数が未設定だと動かないようにしておくと安全です
  }

  const res = await fetch("https://api.example.com/secure-data", {
    headers: {
      Authorization: `Bearer ${apiKey}`,
      // 秘密のキーをヘッダーに付けてAPIを呼びます（この処理はサーバーでだけ行われます）
    },
    cache: "no-store",
  });

  const data = await res.json();
  return (
    <div>
      <h1>管理用データ</h1>
      {/* ここでは data の「公開してよい部分」だけを表示する、といった設計にします */}
      <pre>{JSON.stringify(data, null, 2)}</pre>
    </div>
  );
}
```

---

## 例題：ブラウザから見てよい値だけ NEXT_PUBLIC_ で渡す

次の例は、**Client Component** で、**公開してよい**環境変数を参照するイメージです。  
`NEXT_PUBLIC_` が付いた変数だけがブラウザに含まれるため、ここには「サイト名」や「公開APIのベースURL」など、漏れても問題ない値だけを入れます。

```tsx
// ファイル名の例: app/components/SiteTitle.tsx
"use client";
// Client Component では、process.env で読めるのは NEXT_PUBLIC_ が付いた変数だけです

export default function SiteTitle() {
  const appName = process.env.NEXT_PUBLIC_APP_NAME;
  // .env に NEXT_PUBLIC_APP_NAME=マイアプリ と書いてあれば、ブラウザでもこの値が使えます
  // 秘密のキーは絶対に NEXT_PUBLIC_ で渡さないでください

  return <h1>{appName ?? "デフォルトのサイト名"}</h1>;
  // 未設定のときは "デフォルトのサイト名" を表示します
}
```

---

## まとめ

- **機密情報**は .env に置き、**NEXT_PUBLIC_** を付けずにサーバー側のコードからだけ参照する。
- **ブラウザに渡してよい値**だけ **NEXT_PUBLIC_** を付ける。外部サービスのキーやシークレットは必ずサーバー側（Route Handler / Server Action / データ層）でのみ使う。
- .env は .gitignore に含め、Gitに上げない。必要な変数名の一覧は .env.example に書いておくと、他の人や環境でも再現しやすい。

次章では、**コンポーネントの設計**（役割の分け方やフォルダの整理）を説明します。
