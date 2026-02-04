# 第5章：データベースとリポジトリ

**この章の目標**  
本番のアプリでは、ユーザーや商品のデータを**データベース**に保存します。  
**リポジトリ**とは、**「データの出し入れ」を担当する層**のことで、サービスから「1件取ってきて」「保存して」と頼まれたときに、データベースにアクセスする役割です。  
この章では、NestJS でデータベースをどう扱うか、そしてリポジトリ（またはサービス内でのデータアクセス）の責務を、初心者向けに説明します。

---

## データベースとは？

**データベース**とは、**データを永続的に保存し、後から検索・更新・削除できる**仕組みです。

- 例：ユーザー一覧、商品情報、注文履歴 など  
- メモリ上の配列はサーバーを再起動すると消えますが、データベースに保存しておけば**残ります**。

NestJS では、**TypeORM** や **Prisma** などの **ORM（Object-Relational Mapping）** を使い、「TypeScript のオブジェクト」と「データベースのテーブル」を対応づけて扱うことが多いです。

---

## エンティティとは？

**エンティティ**とは、**データベースの1つのテーブル（または1行）と対応する型・クラス**のことです。

- 例：User エンティティ = users テーブルの1行を表す。id, name, email などのプロパティを持つ。  
- ORM は、エンティティの定義をもとに「テーブルを作る」「行を挿入する」「行を取得する」といった操作を行います。

NestJS + TypeORM では、**@Entity()** を付けたクラスがエンティティになります。

---

## リポジトリパターンとは？

**リポジトリ**とは、**「データの取得・保存・更新・削除」を担当する層**のことです。

- サービスは「ビジネスロジック」（「在庫が足りるか」「割引を適用するか」など）に集中し、**「データベースから取ってきて」「保存して」**はリポジトリ（または ORM のリポジトリ）に任せます。  
- こうすると、**「データの出し入れ」のやり方を変えても**（別の DB に変えても）、サービス側のコードをあまり変えずに済みます。

TypeORM では、**Repository<T>** をサービスに注入し、**find() / findOne() / save() / remove()** などのメソッドでデータベースを操作します。

---

## 例題：TypeORM のエンティティ（User）

次の例は、**users** テーブルと対応する **User** エンティティの定義です。  
TypeORM のデコレータで「このプロパティはカラム」「主キー」「自動採番」などを指定しています。  
各行にコメントを付けています。

```typescript
// ファイル名: src/users/entities/user.entity.ts
import {
  Entity,
  Column,
  PrimaryGeneratedColumn,
  CreateDateColumn,
  UpdateDateColumn,
} from 'typeorm';
// Entity = このクラスがデータベースのテーブルと対応することを宣言
// Column = このプロパティがテーブルのカラムであることを宣言
// PrimaryGeneratedColumn = 主キーで、値は自動採番（UUID や連番）
// CreateDateColumn / UpdateDateColumn = 作成日時・更新日時を自動で管理

@Entity('users')
// テーブル名は 'users'。クラス名は User で、テーブル名だけ別名にしている
export class User {
  @PrimaryGeneratedColumn('uuid')
  // 主キー。'uuid' で UUID が自動採番される（'increment' にすると連番）
  id: string;

  @Column({ type: 'varchar', length: 255 })
  // 文字列型、最大 255 文字
  name: string;

  @Column({ type: 'varchar', length: 255, unique: true })
  // unique: true で、同じメールアドレスは登録できないようにする
  email: string;

  @CreateDateColumn()
  // レコード作成時に自動で現在日時が入る
  createdAt: Date;

  @UpdateDateColumn()
  // レコード更新時に自動で現在日時に更新される
  updatedAt: Date;
}
```

---

## 例題：サービスでリポジトリを注入し、DB を操作する

次の例は、**UsersService** で **TypeORM の Repository<User>** を注入し、**find / findOne / save / remove** でデータベースを操作しているイメージです。  
モジュールで **TypeOrmModule.forFeature([User])** を読み込んでおくと、**@InjectRepository(User)** でリポジトリを注入できます。  
各行にコメントを付けています。

```typescript
// ファイル名: src/users/users.service.ts
import { Injectable, NotFoundException } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
// InjectRepository = TypeORM のリポジトリを DI で注入するためのデコレータ
import { Repository } from 'typeorm';
// Repository<T> = エンティティ T に対する「データの出し入れ」を行う型

import { User } from './entities/user.entity';
import { CreateUserDto } from './dto/create-user.dto';
import { UpdateUserDto } from './dto/update-user.dto';

@Injectable()
export class UsersService {
  constructor(
    @InjectRepository(User)
    private readonly userRepository: Repository<User>,
    // User エンティティ用のリポジトリを注入する。これで DB 操作ができる
  ) {}

  async findAll(): Promise<User[]> {
    return this.userRepository.find();
    // users テーブルから全件取得する
  }

  async findOne(id: string): Promise<User> {
    const user = await this.userRepository.findOne({ where: { id } });
    // where: { id } で「id が一致する1件」を取得する
    if (!user) {
      throw new NotFoundException(`ID ${id} のユーザーは見つかりません`);
    }
    return user;
  }

  async create(createUserDto: CreateUserDto): Promise<User> {
    const user = this.userRepository.create(createUserDto);
    // DTO からエンティティのインスタンスを作る（まだ DB には保存されていない）
    return this.userRepository.save(user);
    // save で DB に挿入し、保存後のエンティティ（id や createdAt が入ったもの）を返す
  }

  async update(id: string, updateUserDto: UpdateUserDto): Promise<User> {
    const user = await this.findOne(id);
    // 既存のユーザーを取得。いなければ findOne 内で NotFoundException が投げられる
    Object.assign(user, updateUserDto);
    // user に updateUserDto のプロパティを上書きする（undefined の項目はそのまま）
    return this.userRepository.save(user);
    // 更新後のエンティティを保存し、返す
  }

  async remove(id: string): Promise<void> {
    const user = await this.findOne(id);
    await this.userRepository.remove(user);
    // remove で DB から削除する
  }
}
```

---

## 例題：モジュールで TypeORM のエンティティを登録する

**UsersModule** で **TypeOrmModule.forFeature([User])** を **imports** に追加すると、このモジュール内で **@InjectRepository(User)** が使えるようになります。  
各行にコメントを付けています。

```typescript
// ファイル名: src/users/users.module.ts
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';

import { UsersController } from './users.controller';
import { UsersService } from './users.service';
import { User } from './entities/user.entity';

@Module({
  imports: [
    TypeOrmModule.forFeature([User]),
    // User エンティティ用のリポジトリをこのモジュールで使えるようにする
  ],
  controllers: [UsersController],
  providers: [UsersService],
  exports: [UsersService],
})
export class UsersModule {}
```

---

## まとめ

- **データベース**にデータを永続化する。NestJS では **TypeORM** や **Prisma** などの ORM を使うことが多い。
- **エンティティ**は、テーブル（または1行）と対応する型・クラス。**@Entity()** と **@Column()** などで定義する。
- **リポジトリ**は「データの出し入れ」を担当する。サービスは **@InjectRepository(Entity)** でリポジトリを注入し、**find / findOne / save / remove** で DB を操作する。
- モジュールで **TypeOrmModule.forFeature([Entity])** を **imports** に登録する。

次章では、**認証・認可**（ログインと「誰が何をしていいか」）を説明します。
