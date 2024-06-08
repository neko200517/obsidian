---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - TypeORM
aliases:
  - <% tp.file.title %>
---
## ORMとは

- オブジェクト指向の言語とRDBの非互換なデータをマッピングする役割
	- オブジェクト指向は「現実世界の物事に即したデータモデル」
	- RDBは「検索などRDBとしての役割を果たすために最適化されたモデル」

## ORMを使うメリット

- SQLを書かなくてもDB操作ができる
- データの定義が1箇所で済むので、メンテナンス性が高まる
- データベースを抽象化できる（プログラマがSQLの方言などを気にしなくてもよくなる）

## ORMを使うデメリット

- ORMライブラリの操作を学ぶ必要がある
- パフォーマンスチューニングが難しい

## NestJSのORMライブラリ

- TypeORM
	- Node.js上で動作するORMライブラリ 
	- TypeScriptで実装されている

## TypeORM 

- Entity
	- RDBのテーブルと対応するオブジェクト 
	- @Entityデコレータをつけたクラスとして定義する
	- @PrimaryGeneratedColumnデコレータや@ColumnデコレータがついたプロパティがRDBのColumnとマッピングされる

```ts
@Entity()
export class Item {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column()
  name: string;
}
```

- Repository
	- Entityを管理するためのオブジェクト 
	- EntityとRepositoryが1対1となり、データベース操作を抽象化する
	- クラスに@EntityRepository()デコレータを付けて、Repositoryを継承する

```ts
@EntityRepository(Item)
export class ItemRepository extends Repository<Item> {
  findById(id: string) {
    return this.findById(id);
  }
}
```
