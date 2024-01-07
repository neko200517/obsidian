## Modifierの基本

- BlockもしくはElementの見た目や振る舞いを定義する
- Modifierは単独で使用できない
- BlockもしくはElementとセットで使用する
- 必須ではない

## Modifierの命名規則

- 単語が1つの場合
	- block__element_modifier
	- 例）menu__item_acrived
- 単語が複数の場合 
	- block-name__element-name_modifier-name 
	- 例）global-nav__link-item_actived-and-focused

## Modifierの種類

- 見た目：サイズ、色
	- 例）
		- button_size_s
		- menu_inline
- 状態：アクティブ、非活性
	- 例）
		- menu__item_actived
		- button_disabled
		- text-input_focused 
- 振る舞い：中央揃え、逆順
	- 例）
		- box_align_center
		- box_flex_reverse
## Modifierのタイプ

- 真偽値：True or False
- キーと値のペア：key_value

## コーディング例

```html
<!-- 見た目を表すModifier -->
<button class="button">ボタン</button>

<button class="button button_size_s">ボタン</button>

<!-- 状態を表すModifier -->
<input type="text" class="text-input" />

<input type="text" class="text-input text-input_error" />

<div class="menu">
	<div class="menu__item">Tab1</div>
	<div class="menu__item menu__item_actived">Tab2</div>
	<div class="menu__item">Tab3</div>
	<div class="menu__item">Tab4</div>
</div>

<!-- 複数のModifier -->
<button class="button button_size_s button_color_primary">ボタン</button>

<!-- NG: 意味が同じ複数のModifierは設定できない -->
<button class="button button_size_s button_size_l">ボタン</button>
```

```css
.button {
	/* buttonで共通するスタイル */
}

.button_size_s {
	/* サイズに関する部分だけ上書きする */
}

.button_color_primary {
	/* 色に関係するスタイルだけに限定し、色以外のプロパティを設定をしない */
}
```

## コーディング例2（詳細度を上げてよい例）

```html
<div class="menu menu_inline">
	<div class="menu__item">Tab1</div>
	<div class="menu__item menu__item_actived">Tab2</div>
	<div class="menu__item">Tab3</div>
	<div class="menu__item">Tab4</div>
</div>
```

```css
.neum_inline {
	/* メニューブロックのデザイン実装（インライン） */
}

/* Blockに対するModifierの子要素Elementは子孫セレクを利用してもよい */
.menu_inline .menu__item {
	/* メニューアイテムのデザイン実装（インライン） */
}
```