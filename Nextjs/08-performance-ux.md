# 第8章：パフォーマンスとUX

**この章の目標**  
「パフォーマンス」＝サイトが**速く・軽く**動くこと、「UX」＝ユーザーが**ストレスなく**使える体験のことです。  
この章では、Next.jsで**画像の扱い**、**重い部分の遅延ロード**、**読み込み中の表示**をどうするとよいかを、初心者向けに説明します。

---

## なぜパフォーマンスとUXを意識するか

- **速い・軽い**  
  画像の最適化や、必要なコードだけを読み込むようにすると、表示が速くなり、通信量も抑えられます。  
  特にスマートフォンでは「重いサイト」は離脱につながりやすいです。

- **読み込み中が分かる**  
  データや画面の準備に時間がかかるとき、「今、読み込み中です」と分かる表示（スピナーやスケルトン）があると、ユーザーは待ちやすくなります。  
  何も表示されないまま止まっていると、「壊れたのでは？」と不安になります。

Next.jsでは、**next/image** で画像を最適化し、**next/dynamic** で重いコンポーネントを遅延ロードし、**loading.tsx** で読み込み中の見た目を用意する、というのがベストプラクティスです。

---

## 画像は next/image で表示する

通常の HTML の `<img>` ではなく、Next.js が用意している **next/image** の **Image** コンポーネントを使うと、

- 画面サイズに合わせた**最適なサイズ**の画像を配信できる  
- **遅延読み込み**（画面に入ってきたら読み込む）がしやすい  
- 画像の形式を最適化してくれる  

といった恩恵があります。

**重要な画像**（トップのヒーロー画像など、最初の画面で見える画像）には **priority** を付けると、「最初から読み込む」と伝えられ、表示が遅れにくくなります。  
それ以外は、priority を付けないことで「必要になったら読み込む」扱いにできます。

---

## 重いコンポーネントは next/dynamic で遅延ロード

- **遅延ロード**  
  「その画面や部品が必要になるまで、そのコードを読み込まない」というやり方です。  
  最初に表示する部分のコード量が減るので、**初回表示が速く**なります。

- **next/dynamic**  
  React のコンポーネントを「動的に import する」ための Next.js の機能です。  
  使うタイミングで読み込むので、**重いライブラリ**や**大きなコンポーネント**に使うと効果的です。

「この部分は最初の画面では使わない」というコンポーネントは、**dynamic** でラップして遅延読み込みすることを検討します。

---

## 読み込み中は loading.tsx で表示する

第1章でも触れたように、**loading.tsx** を置いておくと、そのルート（フォルダ）以下が「読み込み中」のあいだ、自動的にその中身が表示されます。

- スピナー（くるくる回るアイコン）  
- スケルトン（薄いグレーのブロックで、レイアウトの形だけ見せる）  

などを入れておくと、「今、読み込み中です」と伝えられ、UXが良くなります。

---

## 例題：next/image で画像を表示する

次の例では、**next/image** の **Image** を使い、**priority** を付ける場合と付けない場合をコメントで説明しています。  
各行にコメントを付けています。

```tsx
// ファイル名の例: app/components/HeroImage.tsx
// next/image を import します（Next.js が画像を最適化してくれます）
import Image from "next/image";

export default function HeroImage() {
  return (
    <div style={{ position: "relative", width: "100%", height: "400px" }}>
      <Image
        src="/images/hero.jpg"
        // 表示する画像のパス。public/ からの相対パスで指定します
        alt="トップページのメイン画像"
        // スクリーンリーダー用の説明文。アクセシビリティのため必ず書きます
        fill
        // fill を付けると、親要素いっぱいに画像を伸ばします（親に position: relative が必要）
        priority
        // この画像は「最初の画面で見える」重要な画像なので、優先して読み込みます
        // 重要でない画像には priority を付けないと、画面に入ってきたときに読み込まれます（遅延読み込み）
        sizes="100vw"
        // 画面幅いっぱい程度のサイズで表示する、というヒントを渡します
      />
    </div>
  );
}
```

```tsx
// 一覧のサムネイルなど、最初の画面にたくさん並ばない画像の例
import Image from "next/image";

export default function ProductThumb({ src, alt }: { src: string; alt: string }) {
  return (
    <Image
      src={src}
      alt={alt}
      width={200}
      height={200}
      // 幅と高さを指定すると、そのサイズ用に最適化された画像が配信されます
      // priority は付けない → 画面に入ってきたタイミングで読み込まれる（遅延読み込み）
    />
  );
}
```

---

## 例題：next/dynamic で遅延ロードする

次の例では、**重いチャート**を「このページで使うときだけ読み込む」ようにしています。  
**dynamic** で import し、**loading** に「読み込み中」用のコンポーネントを渡しています。  
各行にコメントを付けています。

```tsx
// ファイル名の例: app/dashboard/page.tsx
import dynamic from "next/dynamic";
// dynamic = コンポーネントを「必要になったときに読み込む」ための Next.js の機能です

const HeavyChart = dynamic(
  () => import("@/components/HeavyChart"),
  // import() は「このファイルを、呼ばれたときに読み込む」という意味になります
  {
    loading: () => <p>グラフを読み込み中...</p>,
    // 読み込みが終わるまで、この文言（またはスケルトン）を表示します
    ssr: false,
    // サーバーでは描画しない、という指定（チャートライブラリがブラウザ専用の場合などに使います）
  }
);

export default function DashboardPage() {
  return (
    <div>
      <h1>ダッシュボード</h1>
      {/* HeavyChart は、このページが表示されるときに初めて読み込まれます */}
      <HeavyChart />
    </div>
  );
}
```

---

## 例題：loading.tsx でスケルトンを表示する

次の例は、**app/dashboard/loading.tsx** に置く想定です。  
`/dashboard` 以下のページが「読み込み中」のあいだ、この内容が表示されます。  
各行にコメントを付けています。

```tsx
// ファイル名: app/dashboard/loading.tsx
// このファイルがあると、/dashboard 以下が読み込み中のとき、自動的にこの中身が表示されます

export default function DashboardLoading() {
  return (
    <div style={{ padding: "1rem" }}>
      <div
        style={{
          height: "2rem",
          width: "60%",
          backgroundColor: "#eee",
          borderRadius: "4px",
          marginBottom: "1rem",
        }}
      />
      {/* タイトル部分の「スケルトン」（薄いグレーの棒） */}
      <div
        style={{
          height: "200px",
          width: "100%",
          backgroundColor: "#eee",
          borderRadius: "8px",
        }}
      />
      {/* メインコンテンツ部分のスケルトン */}
      <p style={{ marginTop: "1rem", color: "#666" }}>読み込み中...</p>
    </div>
  );
}
```

---

## まとめ

- **画像**は **next/image** の **Image** を使い、重要な画像だけ **priority** を付ける。
- **重いライブラリやコンポーネント**は **next/dynamic** で遅延ロードを検討する。
- ページ遷移やデータ取得の**読み込み中**は **loading.tsx** でスケルトンやスピナーを用意し、ユーザーに「待っている間」を伝える。

次章では、**エラー処理**（例外の扱い方と、error.tsx でのユーザー向け表示）を説明します。
