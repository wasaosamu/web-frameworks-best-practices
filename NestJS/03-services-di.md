# 第3章：サービスと依存性注入（DI）

**この章の目標**  
**サービス**は、**ビジネスロジック**（「ユーザーを登録する」「商品一覧を取得する」といった実際の処理）を担当するクラスです。  
**依存性注入（DI）**とは、クラスが「自分で new しないで、外から必要なものを渡してもらう」仕組みのことです。  
この章では、サービスの書き方と DI の考え方を、初心者向けに説明します。

---

## サービスとは？

**サービス**とは、**アプリの「中核の処理」を担当するクラス**です。

- 例：ユーザーの登録・取得・更新・削除、商品の在庫チェック、注文の計算 など  
- コントローラーは「どの URL でどのメソッドを呼ぶか」だけを担当し、**中身の処理**はサービスに書きます。  
- そうすることで、**コントローラーは薄く保て**、**処理の再利用**や**テスト**がしやすくなります。

「コントローラー = 窓口」「サービス = 中身の処理」と役割を分ける、と覚えておきましょう。

---

## 依存性注入（DI）とは？

**依存性注入**とは、**あるクラスが使う「別のクラス（依存）」を、そのクラス自身が作らず、外から渡してもらう**仕組みです。

- 例：`UsersController` が `UsersService` を使うとき、Controller の中で `new UsersService()` とは書かず、**コンストラクタの引数**で「UsersService を渡してください」と宣言する。  
- NestJS が、**必要なタイミングで** UsersService のインスタンスを作り、Controller に渡してくれます（**DI コンテナ**が担当）。

こうすると、

- **テストのとき**に、本物の Service の代わりに「モック」を渡しやすい  
- **同じ Service を複数の Controller で共有**できる  
- **どのクラスが何に依存しているか**がコンストラクタを見れば分かる  

というメリットがあります。

---

## @Injectable() の意味

サービスには **@Injectable()** というデコレータを付けます。

- 「このクラスは、NestJS の DI コンテナで**管理される**ので、コンストラクタに書いた依存は**自動で注入してください**」という宣言です。  
- モジュールの **providers** に登録したクラスが、@Injectable() 付きであれば、他のクラスからコンストラクタで要求できます。

---

## 例題：ユーザー用サービス

次の例は、**users.service.ts** で「一覧取得」「1件取得」「作成」「更新」「削除」の**中身の処理**を書いているイメージです。  
ここではまだデータベースは使わず、メモリ上の配列で代用しています。  
各行にコメントを付けています。

```typescript
// ファイル名: src/users/users.service.ts
import { Injectable, NotFoundException } from '@nestjs/common';
// Injectable = DI で注入可能なクラスであることを宣言する
// NotFoundException = 「見つかりません」という HTTP エラーを返すための例外

import { CreateUserDto } from './dto/create-user.dto';
import { UpdateUserDto } from './dto/update-user.dto';
// リクエストの「形」を表す DTO（第4章で説明）

@Injectable()
// このクラスは NestJS の DI コンテナで管理され、必要に応じて注入される
export class UsersService {
  private users: Array<{ id: string; name: string; email: string }> = [];
  // 例として、メモリ上にユーザー一覧を保持する配列を用意（本番では DB を使う）

  findAll() {
    return this.users;
    // 一覧取得：今あるユーザーをそのまま返す
  }

  findOne(id: string) {
    const user = this.users.find((u) => u.id === id);
    if (!user) {
      throw new NotFoundException(`ID ${id} のユーザーは見つかりません`);
      // 見つからないときは 404 を返すための例外を投げる（第7章で扱う）
    }
    return user;
  }

  create(createUserDto: CreateUserDto) {
    const newUser = {
      id: String(Date.now()),
      // 簡単のため、タイムスタンプを ID の代わりにしている（本番では UUID などを使う）
      name: createUserDto.name,
      email: createUserDto.email,
    };
    this.users.push(newUser);
    return newUser;
  }

  update(id: string, updateUserDto: UpdateUserDto) {
    const user = this.findOne(id);
    // 既に findOne で「いなければ 404」を処理しているので、ここでは user が存在する
    if (updateUserDto.name !== undefined) user.name = updateUserDto.name;
    if (updateUserDto.email !== undefined) user.email = updateUserDto.email;
    return user;
  }

  remove(id: string) {
    const index = this.users.findIndex((u) => u.id === id);
    if (index === -1) {
      throw new NotFoundException(`ID ${id} のユーザーは見つかりません`);
    }
    this.users.splice(index, 1);
    // 配列から 1 件削除する。戻り値は使わず、削除したことを示すために void やメッセージを返してもよい
  }
}
```

---

## 例題：コントローラーでサービスを注入する

コントローラー側では、**コンストラクタの引数**でサービスを要求します。  
NestJS が自動で **UsersService** のインスタンスを渡してくれるので、**new** は書きません。

```typescript
// ファイル名: src/users/users.controller.ts（該当部分のみ）
import { Controller, Get, Param } from '@nestjs/common';
import { UsersService } from './users.service';

@Controller('users')
export class UsersController {
  constructor(private readonly usersService: UsersService) {}
  // private readonly を付けると、このクラス内で this.usersService として使える
  // NestJS が UsersModule の providers に登録された UsersService をここに注入する

  @Get()
  findAll() {
    return this.usersService.findAll();
    // 処理の中身はサービスに任せ、コントローラーは「呼ぶだけ」にしている
  }
}
```

---

## まとめ

- **サービス**はビジネスロジック（中身の処理）を担当する。コントローラーは「どの URL でどのメソッドを呼ぶか」に集中する。
- **依存性注入（DI）**では、クラスは「必要なもの」をコンストラクタで受け取り、自分で new しない。NestJS の DI コンテナが注入を担当する。
- サービスには **@Injectable()** を付け、モジュールの **providers** に登録する。コントローラーではコンストラクタでサービスを要求する。

次章では、**DTO とバリデーション**（リクエストの形の定義とチェック）を説明します。
