# 第7章：型安全性

**この章の目標**  
Next.jsでは多くの場合 **TypeScript** を使って開発します。  
TypeScriptの「**型**」を使うと、「この変数には数値しか入らない」「この関数は文字列を返す」といった**約束**をコードで表現でき、**間違いに早く気づける**ようになります。  
この章では、型の基本と、Next.jsで型をどう使うとよいかを、初心者向けに説明します。

---

## 型とは？（簡単なイメージ）

**型**とは、「このデータは何であるか」をプログラムに教えておく**ラベル**のようなものです。

- 例：「この変数には**数値**しか入れない」「この関数は**文字列**を返す」「このオブジェクトには **id と name** が必ずある」  
- 型を書いておくと、  
  - エディタが「ここには数値を入れるべきなのに文字列を入れた」と教えてくれる  
  - 実行する前に**ミスを減らせる**  

というメリットがあります。

Next.jsでは、**strict: true** を基本にし、**any**（「どんな型でもよい」と諦める型）は極力使わないようにすると、より安全なコードになります。

---

## strict: true と any について

- **strict: true**  
  TypeScriptの設定で、「型のチェックを厳しくする」という意味です。  
  未定義のプロパティにアクセスしたり、null の可能性を無視したりすると、コンパイル時にエラーにしてくれます。  
  ベストプラクティスとしては、**strict: true** を有効にしておくことが推奨されます。

- **any**  
  「どんな型でもよい」という型です。  
  any にすると、その部分については型チェックがほとんど効かなくなり、 typo や勘違いに気づきにくくなります。  
  本当にやむを得ない場合だけ使い、**なぜ any にしたか**をコメントで残すと、あとで読み返したときに安心です。

---

## APIの入出力に型を通す

「APIが返すデータの形」や「APIに送るデータの形」を、**型**または **zod のスキーマ**で定義し、  
**呼び出し側までその型が伝わる**ようにすると、安全です。

- 型を定義する  
  - 例：`type Product = { id: number; title: string; price: number };`  
  - こうすると、`Product` を使うところでは「id, title, price がある」前提で補完やチェックが効きます。  
- zod を使う  
  - 「実行時に本当にその形か」をチェックできるので、APIの**レスポンス**を信頼する前に zod で検証すると、より堅牢になります。  
  - zod のスキーマから TypeScript の型を生成することもできます。

「APIの入出力 = 型（またはzod）で定義し、呼び出し側まで型を通す」と覚えておきましょう。

---

## 例題：型を定義してコンポーネントで使う

次の例では、**商品（Product）** の形を型で定義し、props や fetch の結果にその型を付けています。  
各行にコメントを付けています。

```tsx
// ファイル名の例: app/products/page.tsx
// 商品の「形」を型で定義します。id, title, price が必須で、それぞれ number, string, number です
type Product = {
  id: number;
  title: string;
  price: number;
};

export default async function ProductsPage() {
  const res = await fetch("https://api.example.com/products", {
    next: { revalidate: 3600 },
  });

  if (!res.ok) throw new Error("取得に失敗しました");

  const products: Product[] = await res.json();
  // res.json() の結果を「Product の配列」として扱う、と明示しています
  // これにより、このあと products を使うときに id, title, price が補完され、typo を防げます

  return (
    <ul>
      {products.map((product) => (
        <li key={product.id}>
          {/* product は Product 型なので、.title や .price が補完され、存在しないプロパティを書くとエラーになります */}
          {product.title} — {product.price}円
        </li>
      ))}
    </ul>
  );
}
```

---

## 例題：zod でAPIの入力を検証し、型と組み合わせる

次の例は、**APIに送るデータ**を zod で検証し、型安全にしているイメージです。  
zod の **safeParse** で形をチェックし、成功したときだけ `parsed.data` を扱います。  
（zod を入れると、そのスキーマから TypeScript の型を `z.infer<typeof スキーマ>` で取り出せます。）

```ts
// ファイル名の例: app/api/contact/route.ts
import { z } from "zod";

// お問い合わせフォームの「形」を zod で定義します（実行時にチェックできます）
const contactSchema = z.object({
  name: z.string().min(1, "名前は必須です"),
  email: z.string().email("正しいメールアドレスを入力してください"),
  message: z.string().min(10, "メッセージは10文字以上で入力してください"),
});

// zod のスキーマから TypeScript の型を生成できます（オプション）
type ContactInput = z.infer<typeof contactSchema>;

export async function POST(request: Request) {
  const body = await request.json();

  const parsed = contactSchema.safeParse(body);
  if (!parsed.success) {
    return Response.json({ error: parsed.error.flatten() }, { status: 400 });
  }

  const { name, email, message }: ContactInput = parsed.data;
  // ここでは name, email, message が「チェック済み」で、型も付いています
  // 実際の送信処理（メール送信など）をここに書きます

  return Response.json({ message: "受け付けました" });
}
```

---

## まとめ

- **型**で「どんなデータか」を明示すると、ミスに早く気づける。**strict: true** を基本とし、**any** は極力避け、使う場合はコメントで理由を残す。
- APIの**入出力**は型または zod スキーマで定義し、**呼び出し側まで型を通す**と安全。

次章では、**パフォーマンスとUX**（画像の扱い、遅延ロード、読み込み表示）を説明します。
