# 第8章：ミドルウェアとガード

**この章の目標**  
**ミドルウェア**とは、**リクエストがコントローラーに届く「前」に実行される共通処理**のことです。  
**ガード**とは、**「このルートにアクセスしていいか」を、コントローラー実行前にチェックする**仕組みです（第6章で認証ガードに触れました）。  
この章では、ミドルウェアとガードの**役割の違い**と、**いつどちらを使うか**を、初心者向けに説明します。

---

## ミドルウェアとは？

**ミドルウェア**とは、**リクエストを受け取ったあと、ルート（コントローラーのメソッド）が実行される「前」に動く処理**のことです。

- 例：リクエストの**ログを取る**、**リクエストIDを付ける**、**特定のパスだけ別の処理をする**  
- **next()** を呼ぶと「次の処理（次のミドルウェアやルート）に進む」、呼ばないとそこで止まります。

NestJS では **NestMiddleware** を実装したクラスを **configure()** で登録し、「どのパスに」「どのミドルウェアを」適用するかを指定します。

---

## ガードとは？（おさらい）

**ガード**とは、**「このルートを実行していいか」を、true / false または例外で決める**仕組みです。

- 例：**JWT が有効か**（認証ガード）、**ロールが admin か**（認可ガード）  
- ガードは **canActivate()** で true を返すと通過、false や例外を投げるとそこで止まり、クライアントには 403 や 401 などが返ります。

ミドルウェアは「前処理」（ログ、リクエストの加工）に使い、**「アクセスしていいか」の判断**はガードに任せると、役割が分かりやすくなります。

---

## ミドルウェアとガードの使い分け

| 用途 | ミドルウェア | ガード |
|------|--------------|--------|
| ログを取る、リクエストIDを付ける | ○ | — |
| 「認証されているか」「権限があるか」をチェック | — | ○ |
| リクエストボディを加工する | ○（注意して使う） | — |
| ルート実行の可否を決める | — | ○ |

「**アクセスしていいか**」はガード、「**前処理やログ**」はミドルウェア、と分けて考えるとよいです。

---

## 例題：ログ用ミドルウェア

次の例は、**リクエストが来たときにメソッドとURLをログに出す**ミドルウェアです。  
**next()** を呼ぶことで、その後のルートまで処理が進みます。  
各行にコメントを付けています。

```typescript
// ファイル名: src/common/middleware/logger.middleware.ts
import { Injectable, NestMiddleware } from '@nestjs/common';
import { Request, Response, NextFunction } from 'express';
// NextFunction = Express の「次の処理に進む」ための関数。next() に対応する

@Injectable()
export class LoggerMiddleware implements NestMiddleware {
  use(req: Request, res: Response, next: NextFunction) {
    const { method, originalUrl } = req;
    // リクエストのメソッド（GET, POST など）と URL を取り出す
    const start = Date.now();
    // 処理開始時刻を記録（後で「何ミリ秒かかったか」を出したい場合に使える）

    res.on('finish', () => {
      const duration = Date.now() - start;
      console.log(`${method} ${originalUrl} ${res.statusCode} - ${duration}ms`);
      // レスポンスが返り終わったタイミングで、メソッド・URL・ステータス・所要時間をログに出す
    });

    next();
    // next() を呼ばないと、この先のルートやミドルウェアに進まない。必ず呼ぶ
  }
}
```

---

## 例題：モジュールでミドルウェアを登録する

ミドルウェアは、**モジュール**の **configure()** で「どのパスに適用するか」を指定して登録します。  
次の例は、**AppModule** で **LoggerMiddleware** をすべてのルート（`*`）に適用しているイメージです。  
各行にコメントを付けています。

```typescript
// ファイル名: src/app.module.ts
import { Module, MiddlewareConsumer, NestModule } from '@nestjs/common';
// MiddlewareConsumer = ミドルウェアを「どのルートに」適用するかを指定するための型
// NestModule = configure() を持つモジュールであることを示すインターフェース

import { LoggerMiddleware } from './common/middleware/logger.middleware';

@Module({
  imports: [UsersModule],
  controllers: [],
  providers: [],
})
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(LoggerMiddleware)
      // LoggerMiddleware を適用する
      .forRoutes('*');
    // forRoutes('*') で「すべてのルート」に適用する。'users' だけにしたい場合は .forRoutes('users')
  }
}
```

---

## 例題：ガードで「認証必須」を宣言する（おさらい）

「このルートはログイン済みでないとアクセスできない」ようにするには、**@UseGuards(JwtAuthGuard)** を付けます。  
ガードは「アクセスしていいか」を判断する役割なので、認証・認可はガードで行います。  
各行にコメントを付けています。

```typescript
// ファイル名: src/users/users.controller.ts（該当部分のみ）
import { Controller, Get, UseGuards } from '@nestjs/common';
import { JwtAuthGuard } from '../common/guards/jwt-auth.guard';

@Controller('users')
@UseGuards(JwtAuthGuard)
// このコントローラー内のすべてのルートで、JWT 認証ガードが動く
// トークンが無効またはなしの場合は 401 になり、コントローラーのメソッドは実行されない
export class UsersController {
  @Get()
  findAll() {
    return this.usersService.findAll();
  }
}
```

---

## まとめ

- **ミドルウェア**は、リクエストがルートに届く「前」に動く共通処理。ログ、リクエストID、ボディの加工などに使う。**next()** を呼ぶと次の処理に進む。
- **ガード**は「このルートを実行していいか」を判断する。認証・認可はガードで行う。**@UseGuards(GuardClass)** でコントローラーやメソッドに付ける。
- 「前処理・ログ」はミドルウェア、「アクセス可否」はガード、と役割を分けると分かりやすい。

次章では、**環境変数と設定**（開発・本番の切り替え、秘密情報の管理）を説明します。
