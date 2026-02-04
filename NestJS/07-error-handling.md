# 第7章：エラーハンドリングと例外フィルター

**この章の目標**  
アプリでは、**「データが見つからない」「権限がない」「リクエストの形がおかしい」** といった**エラー**が発生することがあります。  
NestJS では、**例外**を投げると、それを**統一された形式**でクライアントに返す仕組みがあります。  
この章では、**組み込みの例外**の使い方と、**例外フィルター**で「エラーをどう返すか」をそろえる方法を、初心者向けに説明します。

---

## エラーハンドリングとは？

**エラーハンドリング**とは、**エラーが起きたときに、握りつぶさず、ログに残し、クライアントには分かりやすい形で返す**ことです。

- 開発者向け：**ログ**にスタックトレースやリクエストIDを残し、原因を追いやすくする。  
- ユーザー（クライアント）向け：**HTTP ステータスコード**（404, 400, 500 など）と**メッセージ**を返し、「何が起きたか」「次にどうすればよいか」が分かるようにする。

NestJS では、**例外**を投げると、その例外の種類に応じた**HTTP ステータス**と**レスポンスの形**が自動で決まります。

---

## NestJS の組み込み例外

NestJS は **@nestjs/common** に、よく使う HTTP 用の例外クラスを用意しています。

| 例外クラス | HTTP ステータス | 主な用途 |
|------------|-----------------|----------|
| **BadRequestException** | 400 | リクエストの形がおかしい、バリデーションエラー |
| **UnauthorizedException** | 401 | 認証されていない（トークンなし・無効） |
| **ForbiddenException** | 403 | 認証はされているが、権限がない |
| **NotFoundException** | 404 | リソースが見つからない（ユーザーIDが存在しないなど） |
| **ConflictException** | 409 | 競合（例：同じメールで既に登録済み） |
| **InternalServerErrorException** | 500 | サーバー内部の想定外エラー |

サービスやコントローラーでは、**throw new NotFoundException('メッセージ')** のように投げると、NestJS が自動で対応する HTTP レスポンスに変換します。

---

## 例外フィルターとは？

**例外フィルター**とは、**アプリ内で投げられた例外をキャッチし、レスポンスの形を統一したり、ログを出したりする**仕組みです。

- 例：すべての例外で **{ statusCode, message, error }** という形の JSON を返す。  
- 例：本番ではスタックトレースをレスポンスに含めず、代わりにログにだけ残す。

NestJS では **ExceptionFilter** を実装したクラスを **@Catch()** で「どの例外を扱うか」を指定し、**app.useGlobalFilters()** でアプリ全体に適用できます。

---

## 例題：サービスで組み込み例外を投げる

「ID でユーザーを取得したが存在しない」ときは **NotFoundException** を投げます。  
NestJS が自動で **404** とメッセージを返します。  
各行にコメントを付けています。

```typescript
// ファイル名: src/users/users.service.ts（該当部分のみ）
import { NotFoundException } from '@nestjs/common';

async findOne(id: string): Promise<User> {
  const user = await this.userRepository.findOne({ where: { id } });

  if (!user) {
    throw new NotFoundException(`ID ${id} のユーザーは見つかりません`);
    // この例外が投げられると、NestJS が 404 と { "message": "..." } のような形でクライアントに返す
  }

  return user;
}
```

---

## 例題：例外フィルターでレスポンスの形を統一する

次の例は、**HttpException**（NestJS の組み込み例外の多くがこれを継承）をキャッチし、**statusCode / message / error** の形で JSON を返すフィルターです。  
本番では **stack**（スタックトレース）をレスポンスに含めないようにしています。  
各行にコメントを付けています。

```typescript
// ファイル名: src/common/filters/http-exception.filter.ts
import {
  ExceptionFilter,
  Catch,
  ArgumentsHost,
  HttpException,
  HttpStatus,
  Logger,
} from '@nestjs/common';
import { Request, Response } from 'express';
// ArgumentsHost = 今処理しているリクエストの文脈（Express の req, res などが取れる）

@Catch(HttpException)
// HttpException と、それを継承した例外（NotFoundException など）をこのフィルターで扱う
export class HttpExceptionFilter implements ExceptionFilter {
  private readonly logger = new Logger(HttpExceptionFilter.name);
  // ログを出すための Logger。どのクラスから出たログかが分かる

  catch(exception: HttpException, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();
    const request = ctx.getRequest<Request>();
    const status = exception.getStatus();
    // 例外に紐づいた HTTP ステータス（404, 400 など）を取得する
    const exceptionResponse = exception.getResponse();
    // 例外に紐づいたレスポンス body（メッセージやオブジェクト）を取得する

    const message =
      typeof exceptionResponse === 'string'
        ? exceptionResponse
        : (exceptionResponse as { message?: string | string[] }).message;
    // メッセージが文字列か配列（バリデーションエラーなど）の場合に対応する

    this.logger.warn(
      `${request.method} ${request.url} ${status} - ${JSON.stringify(message)}`,
    );
    // 開発者用：どの URL で何の例外が起きたかをログに残す

    response.status(status).json({
      statusCode: status,
      message,
      error: exception.name,
      // クライアントには statusCode / message / error の形で返す（スタックトレースは含めない）
    });
  }
}
```

---

## 例題：main.ts でグローバルにフィルターを適用する

**main.ts** で **useGlobalFilters** すると、アプリ全体でその例外フィルターが使われます。  
各行にコメントを付けています。

```typescript
// ファイル名: src/main.ts（該当部分のみ）
import { HttpExceptionFilter } from './common/filters/http-exception.filter';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  app.useGlobalFilters(new HttpExceptionFilter());
  // すべてのルートで発生した HttpException が、このフィルターで処理され、形が統一される

  await app.listen(3000);
}
bootstrap();
```

---

## まとめ

- エラーが起きたら**握りつぶさず**、**組み込みの例外**（NotFoundException, BadRequestException など）を投げる。NestJS が自動で HTTP ステータスとレスポンスを返す。
- **例外フィルター**で、レスポンスの形（statusCode, message, error）を統一し、ログを残す。本番ではスタックトレースをレスポンスに含めない。
- サービスやコントローラーでは、**throw new NotFoundException('メッセージ')** のように、状況に合った例外クラスを選んで使う。

次章では、**ミドルウェアとガード**（リクエスト前の共通処理と、アクセス制御）を説明します。
