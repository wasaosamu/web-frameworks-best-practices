# 第9章：環境変数と設定

**この章の目標**  
**環境変数**とは、**プログラムの外から渡す設定値**（データベースの接続先、秘密のキー、ポート番号など）のことです。  
**設定（Config）**とは、それらの値をアプリ内で**どう読み出すか**を整理したものです。  
この章では、NestJS で環境変数と設定をどう扱うかを、初心者向けに説明します。

---

## 環境変数とは？

**環境変数**とは、**OS やプロセスに「名前＝値」の形で持たせておく設定**のことです。

- 例：`DATABASE_URL=postgresql://...`、`PORT=3000`、`JWT_SECRET=xxx`  
- ソースコードに**直接書かない**ことで、  
  - **秘密の値**（パスワード、APIキー）が Git に含まれない  
  - **開発用・本番用**で違う値を使い分けられる  

というメリットがあります。

NestJS では、プロジェクトのルートに **.env** を置き、**@nestjs/config** の **ConfigModule** で読み込むことが多いです。

---

## .env と .env.example

- **.env** … 実際の値（パスワード、キーなど）を書いたファイル。**Git には含めず**、**.gitignore** に書いておく。  
- **.env.example** … 「どんな変数名が必要か」の**見本**だけを書いたファイル。値は空やダミーでよい。**Git に含めて**、他の人や環境で「何を設定すればよいか」が分かるようにする。

---

## ConfigModule とは？

**ConfigModule** は、NestJS が用意している「**.env を読み込み、アプリ内で process.env や ConfigService 経由で参照できるようにする**」モジュールです。

- **ConfigModule.forRoot()** で、ルートの .env を読み込む。  
- **ConfigService** を注入すると、**get('変数名')** で環境変数の値を取得できる。  
- **validationSchema** を渡すと、「必須の変数が設定されていない」などで起動時にエラーにできる（**Joi** などを使う）。

---

## 例題：ConfigModule を有効にする

**AppModule** の **imports** に **ConfigModule.forRoot()** を追加すると、.env が読み込まれ、**ConfigService** が使えるようになります。  
各行にコメントを付けています。

```typescript
// ファイル名: src/app.module.ts
import { Module } from '@nestjs/common';
import { ConfigModule } from '@nestjs/config';
// ConfigModule = 環境変数（.env）を読み込み、ConfigService で参照できるようにするモジュール

import { UsersModule } from './users/users.module';

@Module({
  imports: [
    ConfigModule.forRoot({
      isGlobal: true,
      // isGlobal: true にすると、ConfigService をどのモジュールからでも注入できる（import 不要）
      envFilePath: '.env',
      // 読み込む .env ファイルのパス。省略するとルートの .env が使われる
    }),
    UsersModule,
  ],
  controllers: [],
  providers: [],
})
export class AppModule {}
```

---

## 例題：ConfigService で環境変数を読む

サービスやコントローラーでは、**ConfigService** を注入し、**get('変数名')** で環境変数を取得します。  
秘密のキーや DB の URL は、ここで読み出して使います。  
各行にコメントを付けています。

```typescript
// ファイル名: src/users/users.service.ts（該当部分のみ・イメージ）
import { Injectable } from '@nestjs/common';
import { ConfigService } from '@nestjs/config';

@Injectable()
export class UsersService {
  constructor(
    private readonly configService: ConfigService,
    // ConfigService を注入する。ConfigModule が isGlobal: true なら、どこでも使える
  ) {}

  someMethod() {
    const dbHost = this.configService.get<string>('DATABASE_HOST');
    // .env に DATABASE_HOST=localhost と書いてあれば、'localhost' が返る
    const port = this.configService.get<number>('PORT', 3000);
    // 第2引数は「変数が未設定のときのデフォルト値」。PORT がなければ 3000 を使う
    const jwtSecret = this.configService.getOrThrow<string>('JWT_SECRET');
    // getOrThrow は、変数が未設定のときに例外を投げる。必須の設定に使う
  }
}
```

---

## 例題：main.ts でポートを環境変数から読む

**main.ts** では、**ConfigService** を使ってポート番号を環境変数から読み、**listen(port)** に渡すことができます。  
各行にコメントを付けています。

```typescript
// ファイル名: src/main.ts
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { ConfigService } from '@nestjs/config';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  const configService = app.get(ConfigService);
  // NestFactory.create() で作った app から ConfigService を取得する
  const port = configService.get<number>('PORT', 3000);
  // .env の PORT を読む。なければ 3000 を使う

  await app.listen(port);
  // 指定したポートでサーバーを起動する
}
bootstrap();
```

---

## .env.example の例

他の人や環境で「何を設定すればよいか」が分かるように、**.env.example** に変数名だけ書いておきます。  
値はダミーや空でよいです。

```bash
# ファイル名: .env.example（プロジェクトルート）
# このファイルは Git に含めてよい。実際の値は .env に書き、.env は .gitignore に入れる

PORT=3000
DATABASE_URL=postgresql://user:password@localhost:5432/mydb
JWT_SECRET=your-secret-key-here
```

---

## まとめ

- **環境変数**で、秘密の値や接続先などをソースコードに書かず、外から渡す。**.env** に書き、**.gitignore** に含めて Git に上げない。
- **ConfigModule.forRoot()** で .env を読み込み、**ConfigService** の **get() / getOrThrow()** で値を参照する。**isGlobal: true** にすると、どのモジュールからでも ConfigService を注入できる。
- **.env.example** に必要な変数名の一覧を書いておくと、他の人や環境で再現しやすい。

---

これで、NestJS + TypeScript のベストプラクティスを、各章ごとの MD ファイルとして一通りまとめました。  
目次は **README.md** にあります。わからない章があれば、そこから読み返してみてください。
