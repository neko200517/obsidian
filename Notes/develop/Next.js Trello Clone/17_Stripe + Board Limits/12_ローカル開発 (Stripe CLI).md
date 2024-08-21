## Webhookタブに移動

https://dashboard.stripe.com/test/webhooks

![[Pasted image 20240820195618.png]]

## ローカル環境でテストをクリック

![[Pasted image 20240820195654.png]]

![[Pasted image 20240820195717.png]]

## Scoop で Windows にStripe CLI をインストール

https://docs.stripe.com/stripe-cli

```bash
scoop bucket add stripe https://github.com/stripe/scoop-stripe-cli.git
scoop update
scoop install stripe
```

## CLI にログイン

```bash
stripe login
```

## Webhookを起動しておく

```bash
stripe listen --forward-to localhost:3000/api/webhook
```

## シークレットキーをコピー

- Your webhook signing secret is whsec_xxxxxxxxxxx という形式でシークレットキーが出力されるので、値をコピーしておく

![[Pasted image 20240820201502.png]]

## .envファイルにペースト

```ts
STRIPE_WEBHOOK_SECRET=whsec_7e0e9ef38f62090fd7a80306189f7bd8703b389e126d16daa6416d28b51eead8
```

## 適当なカード情報で登録

![[Pasted image 20240820204917.png]]

## webhookが反応して200番を返していたら正常

![[Pasted image 20240820201208.png]]

## Stripe に取引履歴が記録されていることを確認

![[Pasted image 20240820203133.png]]