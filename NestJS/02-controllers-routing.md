# 第2章：コントローラーとルーティング

**この章の目標**  
**コントローラー**は「この URL にリクエストが来たら、この処理を実行する」という**窓口**を定義する場所です。  
**ルーティング**とは、URL（パス）とその処理の**対応づけ**のことです。  
この章では、コントローラーの書き方と、GET / POST / PATCH / DELETE などの**HTTP メソッド**との対応を、初心者向けに説明します。

---

## コントローラーとは？

**コントローラー**とは、**クライアント（フロントや他のサービス）からのリクエストを受け取り、どの処理に渡すかを決める**役割を持つクラスです。

- 例：「GET /users が来たら一覧を返す」「POST /users が来たら新規作成する」  
- 実際の「データの取得や計算」は**サービス**に任せ、コントローラーは「どの URL で」「どのメソッド（サービス）を呼ぶか」に集中します。

「URL と処理の対応表」をコードで書いたものがコントローラー、と考えるとわかりやすいです。

---

## ルーティングと HTTP メソッド

- **ルーティング** … 「このパス（URL の道のり）には、この処理を割り当てる」という対応のことです。  
- **HTTP メソッド** … リクエストの**種類**を表します。  
  - **GET** … データの**取得**（一覧、1件取得など）  
  - **POST** … データの**新規作成**  
  - **PATCH / PUT** … データの**更新**  
  - **DELETE** … データの**削除**

NestJS では、**@Controller('パス')** で「このコントローラーが担当する URL のプレフィックス」を決め、**@Get() / @Post() / @Patch() / @Delete()** で「その中のどのパスで、どの HTTP メソッドを受け付けるか」を書きます。

---

## パラメータ付きのルート（:id など）

「ユーザー ID が 123 のデータを取得したい」のように、URL の一部が**変わる**場合は、**パラメータ**を使います。

- 例：**GET /users/:id** … `:id` の部分が 123 なら、ID が 123 のユーザーを返す  
- NestJS では **@Param('id')** で、その部分の値をメソッドの引数として受け取れます。

---

## 例題：ユーザー用コントローラー

次の例は、**users.controller.ts** で「一覧取得」「1件取得」「作成」「更新」「削除」のルートを定義しているイメージです。  
実際の処理は **UsersService** に任せ、コントローラーは「どの URL で何を呼ぶか」だけを書いています。  
各行にコメントを付けています。

```typescript
// ファイル名: src/users/users.controller.ts
import {
  Controller,
  Get,
  Post,
  Body,
  Patch,
  Param,
  Delete,
} from '@nestjs/common';
// NestJS が用意している「コントローラー用のデコレータ」を読み込む

import { UsersService } from './users.service';
// 実際の処理（一覧取得・1件取得・作成など）はサービスに任せる

@Controller('users')
// このコントローラーが担当する URL のプレフィックスは /users
// つまり、GET /users、POST /users、GET /users/1 などのルートがここに集まる
export class UsersController {
  constructor(private readonly usersService: UsersService) {}
  // コンストラクタで UsersService を受け取る（依存性注入）。readonly で再代入を防ぐ

  @Get()
  // GET /users が来たときにこのメソッドが実行される
  findAll() {
    return this.usersService.findAll();
    // 一覧取得の処理はサービスに任せ、その結果をそのまま返す
  }

  @Get(':id')
  // GET /users/123 のように、:id の部分が変わるルート
  // 注意：他の @Get() より下に書くこと（固定パスは上に、:id は下に）
  findOne(@Param('id') id: string) {
    // @Param('id') で URL の :id の部分を id という引数で受け取る
    return this.usersService.findOne(id);
  }

  @Post()
  // POST /users が来たときにこのメソッドが実行される（新規作成）
  create(@Body() createUserDto: CreateUserDto) {
    // @Body() でリクエストボディを createUserDto として受け取る（第4章で DTO を説明）
    return this.usersService.create(createUserDto);
  }

  @Patch(':id')
  // PATCH /users/123 が来たときにこのメソッドが実行される（更新）
  update(@Param('id') id: string, @Body() updateUserDto: UpdateUserDto) {
    return this.usersService.update(id, updateUserDto);
  }

  @Delete(':id')
  // DELETE /users/123 が来たときにこのメソッドが実行される（削除）
  remove(@Param('id') id: string) {
    return this.usersService.remove(id);
  }
}
// CreateUserDto と UpdateUserDto は、第4章で説明する DTO として別ファイルで定義する
```

---

## 例題：レスポンスのステータスコードを指定する

作成（POST）のときは **201 Created**、削除（DELETE）のときは **204 No Content** を返したい、といった場合は **@HttpCode()** や **@HttpStatus** を使います。  
次の例は、**create** で 201 を返すようにしたものです。

```typescript
// 例：POST で 201 を返す
import { Controller, Post, Body, HttpCode, HttpStatus } from '@nestjs/common';

@Controller('users')
export class UsersController {
  constructor(private readonly usersService: UsersService) {}

  @Post()
  @HttpCode(HttpStatus.CREATED)
  // 通常 POST は 201 Created を返すことが多いので、ここで明示する
  create(@Body() createUserDto: CreateUserDto) {
    return this.usersService.create(createUserDto);
  }
}
```

---

## まとめ

- **コントローラー**は「この URL にこの HTTP メソッドが来たら、この処理を実行する」という窓口を定義する。実際の処理は**サービス**に任せる。
- **@Controller('パス')** でプレフィックスを決め、**@Get() / @Post() / @Patch() / @Delete()** でメソッドごとのルートを書く。
- URL の一部を変数にするときは **:id** のように書き、**@Param('id')** で受け取る。
• Controller：URL に来たリクエストを受け取る窓口
• Routing：URL と処理の対応づけ
• Service：実際のビジネスロジックを書く場所
• @Get / @Post / @Patch / @Delete：HTTP メソッドに対応
• :id：URL の変数部分
• @Param(‘id’)：URL の変数を受け取る
• @Body()：POST/PATCH のデータを受け取る
👉 Controller は “どの URL でどのサービスを呼ぶか” だけを書く場所

次章では、**サービスと依存性注入**（ビジネスロジックの置き場所と、DI の考え方）を説明します。
