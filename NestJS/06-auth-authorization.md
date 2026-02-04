# 第6章：認証・認可

**この章の目標**  
**認証**とは「このリクエストを送っているのは**誰か**」（ログインしているか、トークンは正しいか）を確認することです。  
**認可**とは「その人が**この操作をしていいか**」（管理者だけが削除できる、など）を確認することです。  
この章では、NestJS で認証・認可をどう扱うかの**考え方**と、**ガード**の役割を、初心者向けに説明します。

---

## 認証と認可の違い

| 用語 | 意味 |
|------|------|
| **認証（Authentication）** | 「誰か」を特定すること。ログインして発行された **トークン（JWT など）** が正しければ「このユーザー」と判断する。 |
| **認可（Authorization）** | 「その人がこの操作をしていいか」を判断すること。例：管理者だけがユーザーを削除できる。 |

「まず認証で誰かを特定し、そのうえで認可で権限をチェックする」という順番で考えるとわかりやすいです。

---

## JWT とは？（イメージだけ）

**JWT（ジェイ・ダブリュー・ティー）**とは、**「このユーザーでログイン済みです」という情報を、改ざんされにくい形で持たせた文字列**です。

- ログインに成功したら、サーバーが **JWT** を発行し、クライアントに返す。  
- クライアントは、その後のリクエストで **Authorization** ヘッダーに JWT を付けて送る。  
- サーバーは JWT を検証し、中身（ユーザーID など）を取り出して「誰か」を判断する。

NestJS では **@nestjs/jwt** や **Passport** を使って、JWT の生成・検証を行います。

---

## ガードとは？

**ガード**とは、**「このルート（URL）にアクセスしていいか」を、リクエストがコントローラーに届く前にチェックする**仕組みです。

- 例：**JWT が付いていなければ 401 Unauthorized を返す**（認証ガード）  
- 例：**ロールが「管理者」でなければ 403 Forbidden を返す**（認可ガード）

ガードは **@UseGuards(GuardClass)** でコントローラーやメソッドに付けます。  
「認証済みでないと入れないルート」には **JwtAuthGuard** のようなガードを付ける、という形が一般的です。

---

## 例題：認証ガードのイメージ（JWT を検証する）

次の例は、**JWT が付いているか・有効か**をチェックするガードの**イメージ**です。  
実際のプロジェクトでは **@nestjs/passport** と **passport-jwt** を使うことが多いですが、ここでは「ガードが何をするか」を理解するための簡略版です。  
各行にコメントを付けています。

```typescript
// ファイル名: src/common/guards/jwt-auth.guard.ts（イメージ）
import {
  Injectable,
  CanActivate,
  ExecutionContext,
  UnauthorizedException,
} from '@nestjs/common';
// CanActivate = ガードが「このリクエストを通過させるか」を決めるインターフェース
// ExecutionContext = 今処理しているリクエストの文脈（コントローラー、ハンドラなど）が取れる

@Injectable()
export class JwtAuthGuard implements CanActivate {
  canActivate(context: ExecutionContext): boolean {
    const request = context.switchToHttp().getRequest();
    // 今のリクエストオブジェクト（Express や Fastify の request）を取得する
    const token = request.headers.authorization?.replace('Bearer ', '');
    // Authorization ヘッダーから "Bearer トークン" の形で送られてきたトークンを取り出す

    if (!token) {
      throw new UnauthorizedException('トークンがありません');
      // トークンがなければ 401 を返し、この先のコントローラーには届けない
    }

    // ここで実際には JWT を検証し、payload（ユーザーID など）を取り出して
    // request.user = payload のように付けておく。コントローラーでは request.user で参照できる
    return true;
    // true を返すと「このリクエストは通過してよい」となり、コントローラーが実行される
  }
}
```

---

## 例題：コントローラーでガードを使う

「このルートはログイン済みでないとアクセスできない」ようにするには、**@UseGuards(JwtAuthGuard)** を付けます。  
クラスに付けるとそのコントローラー全体、メソッドに付けるとそのメソッドだけに適用されます。  
各行にコメントを付けています。

```typescript
// ファイル名: src/users/users.controller.ts（該当部分のみ）
import { Controller, Get, UseGuards } from '@nestjs/common';
import { JwtAuthGuard } from '../common/guards/jwt-auth.guard';

@Controller('users')
@UseGuards(JwtAuthGuard)
// このコントローラー内のすべてのルートで、JWT 認証ガードが動く
// つまり、トークンが無効またはなしの場合は 401 になり、コントローラーのメソッドは実行されない
export class UsersController {
  @Get()
  findAll() {
    return this.usersService.findAll();
  }
}
```

---

## 例題：ロール（役割）で認可するイメージ

「管理者だけが削除できる」のように**役割**で制限したい場合は、**カスタムデコレータ**と**認可ガード**を組み合わせます。  
次の例は、**@Roles('admin')** のようなデコレータで「admin ロールが必要」と宣言し、ガードで **request.user** のロールをチェックする**イメージ**です。

```typescript
// ファイル名: src/common/decorators/roles.decorator.ts（イメージ）
import { SetMetadata } from '@nestjs/common';
// SetMetadata = メタデータ（「このルートには admin ロールが必要」などの情報）を付ける

export const ROLES_KEY = 'roles';
// メタデータのキー。ガード側でこのキーを使って「必要なロール」を取り出す

export const Roles = (...roles: string[]) => SetMetadata(ROLES_KEY, roles);
// @Roles('admin') と書くと、このルートには 'admin' ロールが必要、というメタデータが付く
```

```typescript
// ガード側（イメージ）：Reflector で ROLES_KEY のメタデータを読み、request.user.role と比較する
// ロールが一致しなければ ForbiddenException を投げる
```

---

## まとめ

- **認証**は「誰か」を特定する（JWT の検証など）。**認可**は「その人がこの操作をしていいか」を判断する（ロールチェックなど）。
- **ガード**で、リクエストがコントローラーに届く前に「トークンがあるか」「権限があるか」をチェックする。**@UseGuards(JwtAuthGuard)** で認証必須のルートにできる。
- JWT はログイン成功時に発行し、その後のリクエストでは **Authorization** ヘッダーで送る。サーバー側で検証し、**request.user** にユーザー情報を載せておくと、コントローラーやサービスで使える。

次章では、**エラーハンドリングと例外フィルター**（エラーの統一的な扱い方）を説明します。
