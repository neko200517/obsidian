## OOCSSとは？

- Object-Oriented CSS（オブジェクト指向CSS）の略

## OOCSSの原則

- ストラクチャーとスキンの分離
	- ストラクチャー（構造）とスキン（あしらい）を分離して実装する

- コンテナとコンテンツの分離
	- コンテナ（エリア）とコンテンツ（UIモジュール）を分離して実装する

## ストラクチャーとスキンの分離

### 悪い例

ボタンの共通スタイルが分離されておらず、冗長的に実装されている。

```html
<body>
	<div id="main">
		<button class="btn-general">ボタン</button>
		<button class="btn-primary">ボタン</button>
	</div>
</body>
```

```css
#main .btn-general {
	/* 省略 */
}

#main .btn-primary {
	/* 省略 */
}
```

### 良い例

ストラクチャーとスキンを分離したことでコードの冗長化を避けることができる。

```html
<body>
	<div id="main">
		<button class="btn general">ボタン</button>
		<button class="btn primary">ボタン</button>
	</div>
</body>
```

```css
#main .btn {
	/* 共通スタイル（ストラクチャー） */
}

#main .general {
	/* generalのスタイル（スキン） */
}

#main .primary {
	/* primaryのスタイル（スキン） */
}
```

## コンテナとコンテンツの分離 

main（コンテナ）とbutton（コンテンツ）の分離

```html
<body>
	<div id="main">
		<button class="btn general">ボタン</button>
		<button class="btn primary">ボタン</button>
	</div>
	<div id="footer">
		<button class="btn general">ボタン</button>
		<button class="btn primary">ボタン</button>
	</div>
</body>
```

```css
#main {
	/* mainのスタイル */
}

.btn {
	/* 共通スタイル（ストラクチャー） */
}

.general {
	/* generalのスタイル（スキン） */
}

.primary {
	/* primaryのスタイル（スキン） */
}
```