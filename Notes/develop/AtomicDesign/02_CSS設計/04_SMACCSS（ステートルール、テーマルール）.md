## ステートルール

- 状態を変更するために、既存のスタイルを上書き・拡張する
- 状態スタイルはレイアウトやモジュールに割り当てることができる
- 状態スタイルはJavaScriptに依存するという意味を持つ
- 状態スタイルはis-の接頭辞を持つ 
- !importantの使用が可能

```html
<div id="header" class="is-collapsed">
	/* 省略 */
</div>
```

```js
const header = document.getElementById("header");
header.classList.remove("is-collapsed");
```

## テーマルール

- Webサイト全体のレイアウトや色、テキスト処理などを一定の規則に従ってまとめて上書きするもの
- Webサイト全体のテーマカラーを蔵から白に変えるなど
- 既存のあらゆるスタイルが上書きの対象

```css
body.theme-dark {
	background-color: black;
	color: white;
}

.theme-dark #header {
	/* 省略 */
}

.theme-dark #sideArea {
	/* 省略 */
}

.navi-menu li {
	/* 省略 */
}
```