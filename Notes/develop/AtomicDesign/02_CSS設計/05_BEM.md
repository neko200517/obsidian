## BEMとは

- Block, Element, Modifier の頭文字
- ユーザーインタフェースを独立したブロックにすることで、複雑なページでも簡単に素早く開発ができることが特徴
- 設計の設計手法に比べて厳格で強力
- 世界的に有名であり、カバー領域がCSSにとどまらない設計手法 

## BEMの基本原則

- Block, Element, ModifierをまとめてBEMエンティティと呼ぶ
- 要素セレクターやIDセレクターは推奨されない
- クラスセレクターの使用が基本 
- 詳細度は均一に保つ
- クラス名は半角英数字の小文字で、複数の単語はハイフンでつなぐ

```html
<a href="#" class="button">Button</a>
```

```css
a {
	/* 要素セレクタは使わない */
}

a.button {
	/* 詳細度が高いセレクタは使わない */
}

.button {
	/* BEMではクラスセレクタを使う。詳細度を均一に保つ */
}
```

### モディファイア 

```html
<a href="#" class="button button_primary">Button</a>
```

```css
.button.button_primary {
	/* 詳細度が高いのでNG */
}

.button_primary {
	/* 詳細度を均一に保つ */
}
```

### 命名規則

```html
<nav class="global-nav"></nav>
```