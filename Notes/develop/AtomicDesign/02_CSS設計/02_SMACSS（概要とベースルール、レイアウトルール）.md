## SMACSSとは

- Scalable and Modular Architecture for CSS（拡張可能でモジュール的なCSS設計）の略
- CSSのコードの役割に応じてカテゴリ分けしたのが特徴
- プロジェクトにおいて考慮しなければならないCSSのおよそ全体をカバーする規則を持っている
- 規則自体はそこまで厳格ではない
- 柔軟性をもった緩めの規則で開発には向いている
- 実装の指針として緩すぎる場合は、ほかの設計手法と組み合わせるか、別の設計方法のほうが向いている

## SCACSSのカテゴリ分け 

1. ベース
2. レイアウト
3. モジュール 
4. ステート（状態）
5. テーマ

## ベースルール

- プロジェクトにおける標準スタイル定義する
- あまり多く定義しすぎない
- 特定の状況下でのスタイルを定義するIDやクラスセレクタは使わない
- !importantも使用しない
- リセットCSSも含む
- bodyに背景色を指定することを強く推奨

```css
/* bodyの背景色 */
body {
	background-color: #fff;
}

/* 要素セレクタ */
a {
	color: #039;
}

/* 議事要素 */
a:hover {
	color: #03f
}

/* 子セレクタ */
a > img {
	border: 1px solid #039
}

/* 子孫セレクタ */
ul li {
	margin-bottom: 10px;
}
```

## レイアウトルール

- ヘッダーやフッター、メインエリア、サイドエリアなどWebサイトの大枠を構成するモジュールに対するルール
- 画面内で一度しか使われない場面が多いのでIDセレクターによるスタイリングも許容 
- レイアウトルールでクラスセレクタを使う場合、「l-」という接頭辞をつけることを推奨

```css
/* IDセレクタを使ったレイアウト */
#header, #article, #footer {
	width: 500px;
	margin: auto;
}

/* クラスセレクタを使ったレイアウト */
.l-section {
	padding: 20px;
}
```

### 上位にクラスセレクタを適用してレイアウトを変更する場合

```css
/* 上位にクラスセレクタを適用してレイアウトを変更する場合 */
#article {
	float: left;
}

#sidebar {
	float: right;
}

.l-flipped #article {
	float: right;
}

.l-flipped #sidebar {
	float: left;
}
```

#### レイアウト1

```html
<body>
	<div id="article">
		Article Area
	</div>
	<div id="sidebar">
		Sidebar Area
	</div>
</body>
```

#### レイアウト2

レイアウト1を反転したスタイル。このように子要素全てのスタイルを変更したい場合上位のクラスセレクタを定義する。

```html
<body class="l-flipped">
	<div id="article">
		Article Area
	</div>
	<div id="sidebar">
		Sidebar Area
	</div>
</body>
```
