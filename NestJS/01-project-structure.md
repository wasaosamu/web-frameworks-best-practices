# 第1章：プロジェクト構成とモジュール

**この章の目標**  
NestJS では、**フォルダの並び方**と**モジュール**という仕組みで「どの機能がどこにあるか」を整理します。  
この章では、フォルダの分け方とモジュールの役割を、初心者向けに説明します。

---

## プロジェクト構成とは？

**プロジェクト構成**とは、ソースコードを**どのフォルダに、どのファイルで**置くかという**ルール**のことです。

- 例：「ユーザー関連はこのフォルダ」「商品関連はこのフォルダ」「共通で使うものはここ」  
- 分かりやすい構成にしておくと、**あとから修正や追加**がしやすくなります。

NestJS では、**機能ごと**にフォルダを分け、その中に「コントローラー」「サービス」などを置く形が推奨されています。

---

## モジュールとは？

**モジュール**とは、**関連する機能をひとまとまりにした単位**です。

- 例：「ユーザーに関する処理（登録・取得・更新）をまとめたモジュール」「商品に関する処理をまとめたモジュール」  
- 各モジュールは、**どのコントローラー**と**どのサービス**を使うかを宣言します。  
- アプリ全体の入口となる **AppModule** が、それらのモジュールを**まとめて読み込む**形になります。

「1つの機能 = 1つのモジュール」と考えると、設計がしやすくなります。

---

## 推奨されるフォルダ構成の例

```
src/
├── app.module.ts           ← アプリ全体の入口。ここに各モジュールを登録する
├── main.ts                 ← サーバーを起動するファイル
├── users/                  ← ユーザー関連の「機能」をまとめたフォルダ
│   ├── users.module.ts     ← ユーザー用モジュールの宣言
│   ├── users.controller.ts ← URL と処理の対応（第2章で説明）
│   ├── users.service.ts    ← ユーザーに関するビジネスロジック（第3章で説明）
│   ├── dto/                ← リクエストの「形」を定義する DTO（第4章で説明）
│   │   ├── create-user.dto.ts
│   │   └── update-user.dto.ts
│   └── entities/           ← データベースのテーブルと対応する型（第5章で説明）
│       └── user.entity.ts
├── products/              ← 商品関連の機能
│   ├── products.module.ts
│   ├── products.controller.ts
│   ├── products.service.ts
│   └── ...
└── common/                 ← 複数のモジュールで使う共通のもの
    ├── filters/            ← エラー処理など（第7章で説明）
    ├── guards/             ← 認可チェックなど（第8章で説明）
    └── pipes/              ← バリデーションなど（第4章で説明）
```

- **機能ごとにフォルダ**（users, products）を分ける  
- その中に **module / controller / service / dto / entities** を置く  
- **共通で使うもの**は **common/** にまとめる  

という形が、読みやすい構成の基本です。

---

## モジュールの書き方

各モジュールは、**@Module()** というデコレータで「このモジュールが何を含むか」を宣言します。

- **imports** … このモジュールが**使う**他のモジュール  
- **controllers** … このモジュールに属する**コントローラー**（URL の窓口）  
- **providers** … このモジュールに属する**サービス**など（依存性注入で使う）  
- **exports** … 他のモジュールに**公開する**サービスなど（他モジュールの imports で使われる）

---

## 例題：ユーザー用モジュールの定義

次の例は、**users** フォルダに置く **users.module.ts** のイメージです。  
「ユーザー用のコントローラーとサービスをこのモジュールに含める」と宣言しています。  
各行にコメントを付けています。

```typescript
// ファイル名: src/users/users.module.ts
import { Module } from '@nestjs/common';
// NestJS が用意している「モジュールを定義するための道具」を読み込む

import { UsersController } from './users.controller';
// 同じフォルダにある「ユーザー用コントローラー」を読み込む

import { UsersService } from './users.service';
// 同じフォルダにある「ユーザー用サービス」を読み込む

@Module({
  // @Module は「このクラスが NestJS のモジュールです」と宣言するデコレータ
  imports: [],
  // このモジュールが使う他のモジュール（今はなし）
  controllers: [UsersController],
  // このモジュールに含まれるコントローラー。URL の窓口になる
  providers: [UsersService],
  // このモジュールに含まれるサービス。ビジネスロジックを担当する
  exports: [UsersService],
  // 他のモジュールが UsersService を使いたいときに、ここで export すると使える
})
export class UsersModule {}
// このモジュールを AppModule の imports に登録すると、アプリで使われる
```

---

## 例題：アプリ全体のモジュール（AppModule）

次の例は、**app.module.ts** で、上で作った **UsersModule** を読み込むイメージです。  
各行にコメントを付けています。

```typescript
// ファイル名: src/app.module.ts
import { Module } from '@nestjs/common';

import { UsersModule } from './users/users.module';
// ユーザー用モジュールを読み込む

@Module({
  imports: [
    UsersModule,
    // ユーザー関連の URL と処理が、アプリに登録される
    // 他に ProductsModule などがあれば、ここに追加していく
  ],
  controllers: [],
  // アプリ全体で直接使うコントローラーがなければ空でよい
  providers: [],
})
export class AppModule {}
```

---

## まとめ

- **プロジェクト構成**は、機能ごとにフォルダを分け、その中に module / controller / service / dto / entities を置く形が分かりやすい。
- **モジュール**は、関連するコントローラーとサービスをひとまとまりにした単位。**@Module()** で imports / controllers / providers / exports を宣言する。
- アプリの入口は **AppModule**。ここに各機能のモジュールを **imports** で登録する。

次章では、**コントローラーとルーティング**（URL と処理の対応づけ）を説明します。
