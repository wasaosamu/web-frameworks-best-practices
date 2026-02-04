# 第3章：データ取得とAPI

**この章の目標**  
Webサイトでは「データベースや外部サービスからデータを取ってきて、画面に表示する」ことがよくあります。  
Next.jsでは、**読み取り用のデータ**と**書き込み・変更用の処理**をどう分けて書くかが大切です。  
この章では、その考え方と書き方を、ITに知見がない方でも追いやすいように説明します。

---

## 「データ取得」と「API」のイメージ

- **データ取得**  
  「商品一覧」「ユーザー情報」「記事の本文」など、**見るためのデータ**をどこかから取ってきて、画面に表示することです。

- **API（エー・ピー・アイ）**  
  プログラム同士が「データを渡す・受け取る・処理を頼む」ための**窓口**のようなものです。  
  「このURLにこういうリクエストを送ると、こういうデータが返ってくる」という約束がAPIです。

Next.jsでは、

- **読み取り**（データを「見る」）→ 基本的に **Server Component の中で fetch などで取得**  
- **書き込み・変更**（データを「送る」「更新する」）→ **Route Handler（app/api/〇〇/route.ts）** や **Server Actions** にまとめる  

という役割分担が推奨されています。

---

## 読み取りは Server Component の fetch で

「一覧を表示したい」「記事を1件表示したい」といった**見るだけ**のデータは、  
**Server Component** の中で、そのまま **fetch**（データを取りに行く処理）を書くのが基本です。

- わざわざ「GET用のAPIを自分で作り、そのAPIをまた呼ぶ」必要はありません。  
- Server 上でデータを取るので、秘密のキーやデータベースに直接つなぐ処理も安全に書けます。  
- 検索エンジンにも内容が伝わりやすい（SEOに有利）です。

「読み取り専用のデータは、ページ（またはその近くのServer Component）で直接 fetch する」と覚えておきましょう。

---

## 書き込み・変更は Route Handler か Server Actions で

「フォームを送信する」「いいねを付ける」「設定を更新する」のように、**データを送ったり変更したりする**処理は、  
次のどちらかにまとめます。

- **Route Handler**  
  `app/api/〇〇/route.ts` というファイルで、**POST / PATCH / PUT / DELETE** などの「リクエストを受け取る窓口」を用意する方法です。  
  例：`/api/contact` に POST でフォームの内容を送る、など。

- **Server Actions**  
  サーバー上で動く「関数」を定義し、フォームやボタンからその関数を呼び出す方法です。  
  フォーム送信と相性が良く、コードを「送信処理」に寄せて書けます。

どちらを使うかはプロジェクトの好みでよいですが、「変更を加える処理はAPI（Route Handler）か Server Actions のどちらかにまとめる」と整理しやすくなります。

---

## 入力のチェック（バリデーション）と認可

Route Handler や Server Actions では、**必ず**次の2つを行います。

1. **入力のチェック（バリデーション）**  
   送られてきたデータが「想定した形か」「危険な文字が含まれていないか」を確認します。  
   **zod** などのライブラリを使うと、型と一緒に「こういう形でなければならない」というルールを書けます。

2. **認可（誰がやっていいか）のチェック**  
   「ログインしているか」「この操作をしていい権限があるか」を確認します。  
   他人のデータを改ざんしたり、管理者だけの機能を一般ユーザーが呼べたりしないようにするためです。

---

## 例題：Server Component でデータを取得する

次の例は、Server Component の中で **fetch** でデータを取得し、その結果を表示するだけのページです。  
**GET用のAPIを別に作らず**、ページ自身がデータを取っています。  
各行のコメントで「何をしているか」を追えるようにしています。

```tsx
// ファイル名の例: app/products/page.tsx
// "use client" がないので、このファイルは Server Component です

// 商品の形を TypeScript の型で定義しています（第7章で詳しく説明します）
type Product = {
  id: number;
  title: string;
  price: number;
};

// このコンポーネントはサーバーで実行されます
export default async function ProductsPage() {
  // async にすると、この中で await を使って「データが届くまで待つ」ことができます

  const res = await fetch("https://api.example.com/products", {
    // 取得した結果を 1 時間キャッシュする、という指定（第4章で詳しく説明）
    next: { revalidate: 3600 },
  });

  if (!res.ok) {
    // レスポンスが OK でない（404 や 500 など）ときは、エラーとして扱います
    throw new Error("商品データの取得に失敗しました");
  }

  const products: Product[] = await res.json();
  // 返ってきた JSON を Product の配列として解釈します

  return (
    <div>
      <h1>商品一覧</h1>
      <ul>
        {/* 取ってきた商品を1つずつ <li> で表示します */}
        {products.map((product) => (
          <li key={product.id}>
            {product.title} — {product.price}円
          </li>
        ))}
      </ul>
    </div>
  );
}
```

---

## 例題：Route Handler（API）で POST を受け取る

「お問い合わせフォームの内容を送る」ような、**データを送り込む**処理は、Route Handler にまとめます。  
次の例では、POST で送られてきた JSON を zod でチェックし、問題がなければ「受け取った」というレスポンスを返しています。  
実際の保存処理は、コメントのところに後から足せます。

```ts
// ファイル名: app/api/contact/route.ts
// このファイルは /api/contact というURLに対する「窓口」になります

import { NextResponse } from "next/server";
// Next.js が用意している「レスポンスを返すための道具」です
import { z } from "zod";
// zod = データの形をチェックするためのライブラリ（npm でインストールが必要です）

// 「お問い合わせの内容」がどんな形か、ルールを定義します
const contactSchema = z.object({
  name: z.string().min(1, "名前は必須です"),
  email: z.string().email("正しいメールアドレスを入力してください"),
  message: z.string().min(10, "メッセージは10文字以上で入力してください"),
});

// POST リクエストが /api/contact に来たときに実行される関数です
export async function POST(request: Request) {
  try {
    const body = await request.json();
    // リクエストの body を JSON として読みます

    const parsed = contactSchema.safeParse(body);
    // safeParse = ルールに合っているかチェックし、合っていれば結果、合っていなければエラーを返します

    if (!parsed.success) {
      // バリデーションに失敗したときは、400 とエラー内容を返します
      return NextResponse.json(
        { error: parsed.error.flatten() },
        { status: 400 }
      );
    }

    const { name, email, message } = parsed.data;
    // ここで name, email, message は「チェック済みの安全なデータ」として使えます
    // 実際にはここでメール送信やデータベース保存などの処理を書きます
    // 例: await sendEmail({ name, email, message });

    return NextResponse.json({ message: "お問い合わせを受け付けました" });
    // 成功時は 200 とメッセージを返します
  } catch {
    return NextResponse.json(
      { error: "サーバーエラーが発生しました" },
      { status: 500 }
    );
  }
}
```

---

## まとめ

- **読み取り**（一覧・詳細表示など）→ Server Component 内で **fetch** で取得。GET用APIを乱立させない。
- **書き込み・変更**（送信・更新・削除）→ **Route Handler**（`app/api/〇〇/route.ts`）の POST/PATCH/PUT/DELETE か **Server Actions** にまとめる。
- Route Handler / Server Actions では、**zod などで入力チェック**と**認可チェック**を必ず行う。

次章では、**キャッシュと再検証**（「同じデータを何度も取らない」「いつ最新に更新するか」）を説明します。
