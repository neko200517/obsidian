---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - NestJS
  - GraphQL
aliases:
  - <% tp.file.title %>
---
## NestJSの基本要素

- NestJSにおける最もコアとなる要素 
	- Module
	- Controller (Resolver)
	- Service

## モジュールの役割

- 関連するResolverやServiceなどをまとめ、アプリケーションとして利用できるようにNestJSに登録する
- モジュール内部で必要となる外部モジュールをインポートしたり、モジュール内の機能を外部にエクスポートすることもできる
- NestJSアプリケーションには必ず1つ以上のルートモジュールと、0個以上のFeatureモジュールが必要となる

## モジュールの定義方法

- classに@Moduleデコレータを付ける
- @Moduleデコレータのプロパティを記述する
	- imports : モジュール内部で必要な外部モジュールを記述 
	- providers: @Resolver、@Injectableデコレータがついたクラス 
	- exports : 外部のモジュールにエクスポートしたいものを記述

```ts
@Module({
  imports: [PrismaModule],
  providers: [TaskResolver, TaskService],
})
export class TaskModule {}
```

## リゾルバの役割 

- GraphQL APIがどのような処理を行うかを定義 
	- データの取得はQuery、その他（追加・更新・削除など）はMutation
- GraphQLスキーマに定義されたデータを返却する

## リゾルバの定義 

- classに@Resolverデコレータをつける
- 実現したい内容の処理をメソッドとして定義する

```ts
@Resolver()
export class TaskResolver {
  @Query(() => [Task])
  getTasks() {
    // タスク取得処理
  }

  @Mutation(() => Task)
  createTask() {
    // タスク作成処理
  }
}
```

## サービスの役割 

- アプリケーション固有のビジネスロジックを定義する
- リゾルバから呼び出すことでユースケースを実現する
	- リゾルバにビジネスロジックを書いてもプログラムは動作するが、責務ごとに分割することで保守性・拡張性などが上がりよい設計となる

## サービスの定義 

- class に @Injectable デコレータを付ける 
- ビジネスロジックの内容を記述する

## NestJSのアーキテクチャ

![[Pasted image 20240611164525.png]]

## Dependency Injection (DI)

- 依存性の注入
- 依存関係のあるオブジェクトを外部から渡す
- 用途）
	- 本番用とテスト用でインスタンスの切り替え
	- ログの出力先の切り替えなど

## DIを利用する方法 

- Moduleのprovidersに依存される側のクラスを登録する

```ts
@Module({
  providers: [TaskResolver, TaskService],
})
export class TaskModule {}
```

- 依存する側のconstructorで注入される側のクラスを引数として受けとる

```ts
@Resolver()
export class TaskResolver {
  constructor(private readonly taskService: TaskService) {}

  @Query(() => [Task])
  getTasks(): Task[] {
    return this.taskService.getTasks();
  }
}
```