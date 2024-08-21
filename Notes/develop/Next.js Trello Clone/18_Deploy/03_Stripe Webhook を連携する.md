## Stripe 開発者ページの Webhook タブにアクセス

https://dashboard.stripe.com/test/webhooks

![[Pasted image 20240822011821.png]]

## オンラインエンドポイントのエンドポイントを追加ボタンをクリック

![[Pasted image 20240822011905.png]]

## エンドポイントURLにVercelで公開したAPIのエンドポイントを入力する

例）
https://trello-clone-one-red.vercel.app/api/webhook

![[Pasted image 20240822011957.png]]

## リッスンするイベントの選択  - イベントを選択ボタンを押す

![[Pasted image 20240822012226.png]]

## checkout.session.completed を選択

![[Pasted image 20240822012251.png]]

## invoice.payment.succeded を選択

![[Pasted image 20240822012414.png]]

## イベントを追加ボタンをクリック

![[Pasted image 20240822012508.png]]

## 作成された Webhook 詳細画面

![[Pasted image 20240822012629.png]]
## 署名シークレットを表示してコピーする

![[Pasted image 20240822012843.png]]

## Vercel で公開したページの設定画面に移動

![[Pasted image 20240822013025.png]]

## Environment Variables に移動

![[Pasted image 20240822013050.png]]

## STRIPE_WEBHOOK_SECRET に署名シークレットをペースト

![[Pasted image 20240822013230.png]]

## NEXT_PUBLIC_APP_URL に公開したページのURLをペースト

例）
https://trello-clone-one-red.vercel.app/

![[Pasted image 20240822013326.png]]