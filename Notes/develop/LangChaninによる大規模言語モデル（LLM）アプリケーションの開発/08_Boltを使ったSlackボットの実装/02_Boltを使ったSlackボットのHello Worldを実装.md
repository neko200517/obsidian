## Event Subscripts 

### Enable Eventsを有効にする

https://api.slack.com/apps/A06LXSDHM9N/event-subscriptions?

### Subscribe to bot events

Subscribe to bot eventsのAdd Bot User Enventを押下
app_mentionを有効化
Save Changesを押下
reinstall your appを押下→インストールする

## Pythonで必要なパッケージをインストール 

```bash
poetry add slack-bot@1.18.1
```

## Pythonコードの実装

```bash
touch src/slack_app.py
```

```python
import os
from slack_bolt import App
from slack_bolt.adapter.socket_mode import SocketModeHandler
from dotenv import load_dotenv

load_dotenv()

# ボットトークンとソケットモードハンドラーを使ってアプリを初期化します
app = App(token=os.environ.get("SLACK_BOT_TOKEN"))

@app.event("app_mention")
def handle_mention(event, say):
    say("Hello Slack")

# アプリを起動します
if __name__ == "__main__":
    SocketModeHandler(app, os.environ["SLACK_APP_TOKEN"]).start()
```

## Slackボットを実行

```bash
poetry run python src/slack_app.py
```

## Slackにアクセス

https://app.slack.com/client/T06LXS8FXMW/C06LMNG24Q1?geocode=ja-jp

メッセージ欄に/inviteを入力
→slack-langchainを追加

メッセージ欄に@slack-langchain Helloと入力

→Hello Slackと返ってきたらOK
