## Mix

- 単一のDomノードに、異なるBEMエンティティが複数付与されたインスタンス

  ↓

- コードを複製することなく、複数のBEMエンティティの振る舞いやスタイルを組み合わせる
- 既存のBEMエンティティから新しいモジュールを作成する

## 例１

```html
<header class="header">
	<div class="menu header__menu"></div>
	<div class="logo header__log"></div>
	<div class="search header__search"></div>
	<div class="auth header__auth"></div>
</header>
```

```css
/* ×：子孫セレクタを使わない */
.header .logo {
	margin-right: 14px;
}

/* Mixを使ったスタイリング */
.header__logo {
	margin-right: 14px;
}
```

### 例２

```html
<header class="header">
</header>

<footer class="footer">
</footer>
```

```css
/* ×：グループセレクタを使うのは避ける */
.header, .footer {
	font-size: 14px;
}

/* BEMエンティティごとに別にすることで独立性が高まる */
.header {
	font-size: 14px;
}

.footer {
	font-size: 14px;
}
```

## Mixのまとめ

- 詳細度を高めないため
- Blockの再利用性を保つことができる
- Blockの独立性を保つことができる

## 命名規則のカスタマイズ

- BEMの命名規則はBlock, Element, Modifierの単語それぞれが区別できればカスタマイズ可能

 - Modifierの区切りをハイフンふたつにする
	 - block-name__element-name--mod-name--mod-val
- 単語の区切りをキャメルケースにする
	- bockName__elementName_modName_modValue
- Reactスタイル 
	- BlockName-ElemName_ModName_ModValue 

## MindBEMding

- Modifierの区切りはハイフン二つ
- Modifierのキーは省略可能 
	- block-name__elem-name--mod-name

## BEMを成功させるコツ

- DOMモデルではなくBlock単位をベースに考える
- IDセレクターと要素セレクターは使わない
- 子孫セレクターでネストされるセレクターの数は少なくする
- コードから情報が読み取れる名前付けをする
- Block, Element, Modifierの区別を意識する
- Block, Elementで変更が頻繁に起こりそうなプロパティはModifierとして定義しておく
- Mixを積極的に使用する
- 管理性を高めるためにも、Blockはひとつひとつがなるべく小さくなるように分割する
- Blockを積極的に再利用する
