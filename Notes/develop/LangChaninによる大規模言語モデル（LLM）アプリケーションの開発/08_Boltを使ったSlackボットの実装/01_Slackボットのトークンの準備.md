## Slackに登録

https://slack.com/intl/ja-jp/

## Bolt入門ガイド

https://slack.dev/bolt-python/ja-jp/tutorial/getting-started

## OAuth & Permissionでトークン取得

https://api.slack.com/apps/A06LXSDHM9N/oauth?

→左メニューからOAuth & Permissionにアクセス
→ScopesのAdd an OAuth Scopeを押下
→chat:writeを作成
→OAuth Tokens for Your WorkspaceのInstall to Workspaceを押下してワークスペースに権限を付与
→トークンを取得

### .envに追記

```env
SLACK_APP_TOKEN=xxxxxxx
```

## Basic Informationでトークン取得

https://api.slack.com/apps/A06LXSDHM9N/general?

→左メニューからBasicにアクセスする
→App-Level TokensのGenerate Token and Scopeを押下
→connections:writeを作成
→トークンを取得

### .envに追記

```env
SLACK_BOT_TOKEN=xxxxxxx
```

## Socketモードを開いてSocket Modeを有効にする

https://app.slack.com/app-settings/T06LXS8FXMW/A06LXSDHM9N/socket-mode

Enable Socket ModeをONにする
