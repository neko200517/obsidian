## モジュールルール

- レイアウトモジュールの中に配置されるモジュールが対象
- 他のページや別のレイアウトモジュールに移動しても見た目が崩れず、変わらずに使用できることが必要
- IDセレクタは使えないので、クラススタイルを使ってスタイリングする
- なるべく要素セレクタは使わない
- 特定のコンテキストに依存しない

## 例1

#### 悪い例

first-childで定義すると変更に弱いスタイルとなる

```html
<div class="fld">
	<span>Folder Name</span>
	<span>(32 files))</span>
</div>
```

```css
.fld > span:first-child {
	padding-left: 22px;
	background: url(folder.svg) no-repeat left center;
}
```

#### 良い例

クラスで分離することで変更に強くする

```html
<div class="fld">
	<span class="fld-name">Folder Name</span>
	<span class="fld-items">(32 files))</span>
</div>
```

```css
.fld .fld-name {
	padding-left: 22px;
	background: url(folder.svg) no-repeat left center;
}
```

## 例2

#### 悪い例

要素が増えると複雑で管理しづらいスタイルになる

```html
<div id="main">
	<div class="pod">
		<label>Label</label>
		<input type="text">
	</div>
	<div class="pod-callout">
		<label>Label</label>
		<input type="text">
	</div>
</div>
<div id="side">
	<div class="pod">
		<label>Label</label>
		<input type="text">
	</div>
	<div class="pod-callout">
		<label>Label</label>
		<input type="text">
	</div>
</div>
```

```css
.pod {
	width: 100%;
}
.pod input[type="text"] {
	width: 50%;
}
#side .pod input[type="text"] {
	width: 100%;
}
.pod-callout {
	width: 150px;
}
#side .pod-callout input[type="text"],
.pod-callout input[type="text"] {
	width: 100px;
}
```

#### 良い例

単独でスタイルが適用できるようにモジュール化する

```html
<div id="main">
	<div class="pod">
		<label>Label</label>
		<input type="text">
	</div>
	<div class="pod pod-callout">
		<label>Label</label>
		<input type="text">
	</div>
</div>
<div id="side">
	<div class="pod pod-constrained">
		<label>Label</label>
		<input type="text">
	</div>
	<div class="pod pod-callout">
		<label>Label</label>
		<input type="text">
	</div>
</div>
```

```css
```css
.pod {
	width: 100%;
}
.pod input[type="text"] {
	width: 50%;
}
.pod.pod-constrained input[type="text"] {
	width: 100%;
}
.pod.pod-callout {
	width: 150px;
}
.pod.pod-callout input[type="text"] {
	width: 100px;
}
```
```
