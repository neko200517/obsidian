## Blockの基本

- 論理的かつ機能的に独立したページモジュール
- 特定コンテキストに依存しない、どこでも使いまわせるパーツ
- Blockにレイアウトに関するスタイリングをしてはならない

## Blockの命名規則

- 単語が一つの場合
	- block 
	- 例）menu
- 単語が複数の場合 
	- block-name
	- 例）global-nav
- 「それが何なのか」を表す名前に関する 
	- × ```<div class="red-text"></div>```
	- 〇 ```<div class="error"></div>```

## Elementの基本 

- Blockを構成し、Blockの外では独立して使用できないもの
- Elementはネスとしてもよい
- Elementは必ずBlock内に配置する
- Elementはなくてもよい

## Elementの命名規則

- 単語が一つの場合 
	- block__element （アンダーバー2つ）
	- 例）menu__item
- 単語が複数の場合 
	- block-name__element-name 
		- 例）global-nav__link-item 
- 「それが何なのか」を表す名前に関する 

## コーディング例

```html
<!-- 単語1つでElementがないBlock -->
<button class="button">ボタン</button>

<!-- 複数の単語でElementがないBlock -->
<input type="text" class="text-input" />

<!-- 単語1つでElementがあるBlock -->
<div class="menu">
	<div class="menu__item">Tab1</div>
	<div class="menu__item">Tab2</div>
	<div class="menu__item">Tab3</div>
	<div class="menu__item">Tab4</div>
</div>

<!-- 複数単語でElementがネストしているBlock -->
<nav class="global-nav">
	<ul class="global-nav__list">
		<li class="global-nav__list-item">
			<a href="#" class="global-nav__link">ページ1</a>
		</li>
		<li class="global-nav__list-item">
			<a href="#" class="global-nav__link">ページ2</a>
		</li>
		<li class="global-nav__list-item">
			<a href="#" class="global-nav__link">ページ3</a>
		</li>
	</ul>
</nav>
```

```css
.menu {
	/* Blockのスタイル */
}

.menu .menu__item {
	/* ×：BlockとElementが子孫セレクタで表現されている。詳細度が均一になっていない */
}

.menu__item {
	/* 〇：Htmlのネストに関わらず、詳細度は均一にする */
}
```