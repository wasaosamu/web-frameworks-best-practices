# 第6章：コンポーネント設計

**この章の目標**  
Next.jsやReactでは、画面を「**コンポーネント**」という小さな部品に分けて作ります。  
この章では、**どう分けると分かりやすく保守しやすいか**を、高校卒業したばかりでITに知見がない方にも伝わるように説明します。

---

## コンポーネントとは？

**コンポーネント**とは、「見た目と役割がひとまとまりになった部品」のことです。

- 例：ボタン、カード、ヘッダー、フォーム、一覧の1行……  
- これらを**名前の付いた部品**として定義し、必要なところで「この部品をここに置く」と書いていきます。

部品化すると、

- **同じ見た目を何度も使い回せる**  
- **どのファイルに何が書いてあるか**が分かりやすくなる  
- **修正するとき**、該当する部品だけ直せばよい  

というメリットがあります。

---

## 「props in → JSX out」の純粋なUIコンポーネント

**純粋なUIコンポーネント**とは、

- **渡されたデータ（props）** を受け取る  
- そのデータをもとに **表示（JSX）** を返す  
- その場で「APIを呼ぶ」「グローバルな状態を書き換える」といった**余計な副作用**は持たない  

という形のコンポーネントです。  
「props in → JSX out」と覚えるとわかりやすいです。

こうすると、

- テストしやすい（渡したpropsに対して、期待する表示が出るかだけ見ればよい）  
- どこで使っても挙動が予測しやすい  
- 状態やAPI呼び出しは、**別のレイヤー**（ページやカスタムフック）に寄せられる  

ので、設計がきれいになります。

---

## 責務を分ける：components / features / hooks / dal

プロジェクトが大きくなってきたら、**役割ごとにフォルダを分ける**と整理しやすくなります。

| フォルダ（例） | 役割のイメージ |
|----------------|----------------|
| **components/** | 汎用的なUI部品（ボタン、カード、入力欄など）。多くの画面で使い回すもの。 |
| **features/** | 機能ごとのまとまり（例：ログイン、商品一覧、カート）。その機能に特化したコンポーネントやロジックを置く。 |
| **hooks/** | 再利用したい「ロジック」を **use〇〇** というカスタムフックにまとめたもの。状態や副作用をここに寄せる。 |
| **dal/** や **lib/** | データアクセスや、純粋な計算・ユーティリティ関数。コンポーネントから「データを取ってきて」「計算して」と頼むときに使う。 |

- **責務を跨がない**＝「このファイルは表示だけ」「このファイルはデータ取得だけ」のように、1ファイルに「やることが1つ」に近づける、という意味です。

---

## 再利用したいロジックは Hook や lib に切り出す

- **カスタムフック（useXxx）**  
  「状態の更新」「APIを呼んで結果をstateに入れる」など、**ロジック**をまとめた関数です。  
  コンポーネントの中に長いロジックを書かず、`useProducts()` のように「このHookを使えば商品一覧の取得と状態がまとめて手に入る」という形にすると、複数の画面で使い回せます。

- **lib/ の純粋関数**  
  入力を受け取って計算や変換の結果を返すだけの関数（同じ入力なら必ず同じ出力になるもの）は、**lib/** などに置きます。  
  日付のフォーマット、金額の表示用変換、バリデーションなどが該当します。

---

## 例題：純粋なUIコンポーネント（props in → JSX out）

次のコンポーネントは、**渡された props だけ**を使って表示しています。  
内部でAPIを呼んだり、useStateで状態を持ったりしていません。  
各行にコメントを付けています。

```tsx
// ファイル名の例: components/ProductCard.tsx
// 商品1件分の「カード」表示をする、純粋なUIコンポーネントです

type ProductCardProps = {
  title: string;   // 商品名
  price: number;   // 価格
  imageUrl?: string;  // 画像URL（なくてもよいので ? を付けています）
};

// props を受け取り、それに合わせたJSXを返すだけ。副作用はありません
export default function ProductCard({ title, price, imageUrl }: ProductCardProps) {
  return (
    <div style={{ border: "1px solid #ccc", padding: "1rem", borderRadius: "8px" }}>
      {/* 画像があるときだけ表示します */}
      {imageUrl && (
        <img src={imageUrl} alt={title} style={{ width: "100%", height: "auto" }} />
      )}
      <h3>{title}</h3>
      <p>{price.toLocaleString()}円</p>
      {/* toLocaleString() で千桁のカンマを付けています（例: 1000 → "1,000"） */}
    </div>
  );
}
```

---

## 例題：ロジックを Hook に切り出す

次の例は、「商品一覧を取得する」という**ロジック**を **useProducts** というカスタムフックにまとめたイメージです。  
コンポーネント側は「Hookを呼んで、結果を表示する」だけにできます。  
このHookはClient Componentで使う想定なので、Hookを利用するコンポーネントには `"use client"` が必要です。

```tsx
// ファイル名の例: hooks/useProducts.ts
"use client";
// カスタムフックでは useState や useEffect を使うため、このファイルはクライアント用です

import { useState, useEffect } from "react";

type Product = {
  id: number;
  title: string;
  price: number;
};

export function useProducts() {
  const [products, setProducts] = useState<Product[]>([]);
  // 商品一覧を保持する状態。初期値は空の配列です
  const [loading, setLoading] = useState(true);
  // 「いま読み込み中か」を表す状態。最初は true にしておきます
  const [error, setError] = useState<Error | null>(null);
  // エラーが起きたら、ここにエラーを入れて呼び出し元で表示できます

  useEffect(() => {
    fetch("https://api.example.com/products")
      .then((res) => res.json())
      .then((data) => {
        setProducts(data);
        setLoading(false);
        // データが取れたら products を更新し、loading を false にします
      })
      .catch((err) => {
        setError(err);
        setLoading(false);
        // 失敗したら error をセットし、loading を false にします
      });
  }, []);
  // 空の依存配列 [] なので、マウント時に1回だけ実行されます

  return { products, loading, error };
  // 呼び出し元は「商品一覧」「読み込み中か」「エラー」の3つを受け取れます
}
```

```tsx
// ファイル名の例: features/products/ProductList.tsx
"use client";

import { useProducts } from "@/hooks/useProducts";
import ProductCard from "@/components/ProductCard";

export default function ProductList() {
  const { products, loading, error } = useProducts();
  // ロジックは useProducts に任せ、このコンポーネントは「表示」に集中します

  if (loading) return <p>読み込み中...</p>;
  if (error) return <p>エラーが発生しました</p>;

  return (
    <div style={{ display: "flex", flexWrap: "wrap", gap: "1rem" }}>
      {products.map((product) => (
        <ProductCard
          key={product.id}
          title={product.title}
          price={product.price}
        />
      ))}
    </div>
  );
}
```

---

## まとめ

- UIコンポーネントはできるだけ「**props in → JSX out**」の純粋な形にし、副作用はHookやページに寄せる。
- 役割ごとに **components / features / hooks / lib（またはdal）** などを分け、責務を跨がないようにする。
- 再利用したいロジックは **useXxx** カスタムフックや **lib/** の純粋関数として切り出す。

次章では、**型安全性**（TypeScriptでミスを減らす書き方）を説明します。
