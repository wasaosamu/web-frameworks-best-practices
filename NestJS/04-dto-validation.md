# 第4章：DTO とバリデーション

**この章の目標**  
**DTO（Data Transfer Object）**とは、**クライアントから送られてくるデータの「形」**を定義するためのクラスや型のことです。  
**バリデーション**とは、そのデータが「想定した形か」「危険な値が含まれていないか」を**チェックする**ことです。  
この章では、DTO の書き方と、NestJS でバリデーションをかける方法を、初心者向けに説明します。

---

## DTO とは？

**DTO**とは、**データをやり取りするときの「形」を決めておくもの**です。

- 例：「ユーザー登録のリクエストには、name と email が必須」「更新のときは name だけ送ってよい」  
- DTO を定義しておくと、  
  - **型**で「どんなプロパティがあるか」が分かる  
  - **バリデーション**で「必須か」「形式は正しいか」をチェックできる  

というメリットがあります。

NestJS では、**クラス**で DTO を定義し、**class-validator** のデコレータで「必須」「メール形式」などのルールを書くことが多いです。

---

## バリデーションとは？

**バリデーション**とは、送られてきたデータが**ルールに合っているか**を確認することです。

- 例：名前が空でないか、メールアドレスが正しい形式か、文字数が上限を超えていないか  
- バリデーションに**失敗した**ときは、**400 Bad Request** とエラーメッセージを返し、サービスには**渡さない**ようにします。

NestJS では、**ValidationPipe** を有効にしておくと、DTO に付けた **class-validator** のルールが自動で実行され、違反があれば 400 を返してくれます。

---

## class-validator と ValidationPipe

- **class-validator** … クラスのプロパティに「必須」「メール形式」「最小文字数」などの**ルール**をデコレータで付けるライブラリです。  
- **ValidationPipe** … NestJS が用意している「リクエストボディを DTO の形に変換し、class-validator でチェックする」パイプです。  
  - **main.ts** で `app.useGlobalPipes(new ValidationPipe())` のように有効にしておくと、すべてのルートで DTO のバリデーションが動きます。

---

## 例題：作成用 DTO（CreateUserDto）

次の例は、**ユーザー新規作成**のリクエストボディの形を定義した DTO です。  
**class-validator** のデコレータで「必須」「メール形式」などを指定しています。  
各行にコメントを付けています。

```typescript
// ファイル名: src/users/dto/create-user.dto.ts
import { IsString, IsEmail, IsNotEmpty, MinLength } from 'class-validator';
// IsString = 文字列であること
// IsEmail = メールアドレスの形式であること
// IsNotEmpty = 空でないこと（空文字や null を禁止）
// MinLength = 最小文字数

export class CreateUserDto {
  @IsString()
  // このプロパティは文字列でなければならない
  @IsNotEmpty({ message: '名前は必須です' })
  // 空文字や null は不可。違反したときのメッセージを指定できる
  name: string;

  @IsEmail({}, { message: '正しいメールアドレスを入力してください' })
  // メールアドレスの形式でなければならない
  @IsNotEmpty({ message: 'メールアドレスは必須です' })
  email: string;
}
```

---

## 例題：更新用 DTO（UpdateUserDto）— オプショナル

**更新**のときは「変更したい項目だけ送る」ことが多いので、**すべてのプロパティをオプショナル**にした DTO を使います。  
**class-validator** の **@IsOptional()** を付けると、「値が渡されていればチェックする、渡されていなければスキップ」になります。

```typescript
// ファイル名: src/users/dto/update-user.dto.ts
import { IsString, IsEmail, IsOptional, MinLength } from 'class-validator';

export class UpdateUserDto {
  @IsOptional()
  // このプロパティは「送られていれば」チェックする。送られていなければスキップ
  @IsString()
  @MinLength(1, { message: '名前は1文字以上で入力してください' })
  name?: string;
  // ? で「省略可能」であることを型でも表す

  @IsOptional()
  @IsEmail({}, { message: '正しいメールアドレスを入力してください' })
  email?: string;
}
```

---

## 例題：main.ts で ValidationPipe を有効にする

**main.ts** で **ValidationPipe** をグローバルに有効にすると、すべてのルートで DTO のバリデーションが動きます。  
次の例では、**whitelist: true** で「DTO に定義されていないプロパティは捨てる」ようにしています。  
各行にコメントを付けています。

```typescript
// ファイル名: src/main.ts
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { ValidationPipe } from '@nestjs/common';
// ValidationPipe = リクエストボディを DTO に変換し、class-validator でチェックするパイプ

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  app.useGlobalPipes(
    new ValidationPipe({
      whitelist: true,
      // DTO に定義されていないプロパティは、リクエストボディから削除する（セキュリティ上有利）
      forbidNonWhitelisted: true,
      // DTO にないプロパティが送られてきたら 400 を返す（厳格モード）
      transform: true,
      // クエリやパラメータを、DTO の型（number など）に自動変換する
    }),
  );

  await app.listen(3000);
}
bootstrap();
```

---

## まとめ

- **DTO**は、リクエスト（やレスポンス）の「形」を定義する。NestJS ではクラスで書き、**class-validator** のデコレータでルールを付ける。
- **バリデーション**で「必須」「形式」「文字数」などをチェックする。**ValidationPipe** をグローバルに有効にすると、DTO のルールが自動で実行される。
- 作成用 DTO は必須項目を定義し、更新用 DTO は **@IsOptional()** でオプショナルにする。

次章では、**データベースとリポジトリ**（データの永続化と責務の分離）を説明します。
