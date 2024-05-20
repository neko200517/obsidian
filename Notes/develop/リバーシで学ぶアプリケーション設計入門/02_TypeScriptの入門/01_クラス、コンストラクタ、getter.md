---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - typescript
  - express
  - 設計
  - オブジェクト指向
  - DI
  - 3層アーキテクチャ
  - デザインパターン
aliases:
  - <% tp.file.title %>
---

## クラス、コンストラクタ

```ts
// クラス定義
class Fraction {
  // メンバ
  numerator: number;
  denominator: number;

  // コンストラクタ
  constructor(numerator: number, denominator: number) {
    this.numerator = numerator;
    this.denominator = denominator;
  }
}

// 使い方
const f1 = new Fraction(1, 2);
```

## getter

変更点：

- プロパティに private を追加
- 各プロパティに getter を定義

```ts
// クラス定義
class Fraction {
  // メンバ
  private _numerator: number;
  private _denominator: number;

  // コンストラクタ
  constructor(numerator: number, denominator: number) {
    this._numerator = numerator;
    this._denominator = denominator;
  }

  // getter: numerator
  get numerator() {
    return this._numerator;
  }

  // getter: denominator
  get denominator() {
    return this._denominator;
  }
}

// 使い方
const f1 = new Fraction(1, 2);

console.log(f1.numerator);
console.log(f1.denominator);
```

## コンストラクタの省略

コンストラクタで内部のメンバに値を設定する使い方には省略する書き方がある
変更点

- クラスメンバを削除
- コンストラクタにメンバと同じ定義を書く
- コンストラクタの内容を削除

```ts
// クラス定義
class Fraction {
  // コンストラクタ
  constructor(private _numerator: number, private _denominator: number) {}

  // 中略
}
```
