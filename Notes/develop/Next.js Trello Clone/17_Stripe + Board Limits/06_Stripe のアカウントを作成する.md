## URL

https://stripe.com/jp

## Stripe のトップページ

- 事前にアカウントを作成しておく
- 開発用のためプロフィールは後で設定する

![[Pasted image 20240820175559.png]]

## ダッシュボードに移動する

![[Pasted image 20240820175718.png]]

## 左上のメニューから新規アカウントの作成をクリック

![[Pasted image 20240820175756.png]]

## アカウント名を入力

- 名前: trello-clone

![[Pasted image 20240820175808.png]]

## 開発者用画面へ移動 

- 右上メニューに開発者リンクがあるのでクリック

![[Pasted image 20240820175956.png]]

![[Pasted image 20240820180010.png]]

## API タブへ移動 

- シークレットキーをコピーする

![[Pasted image 20240820180407.png]]

## .envファイルにシークレットキーをペースト

```ts
STRIPE_API_KEY=sk_test_xxxxxxxx
```