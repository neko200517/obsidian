## slack_app.pyをデプロイ用に編集

APP_ENVがproductionの場合の場合のみソケットモードではなくAPIモードで起動するように変更

```python
import os
from slack_bolt import App
from slack_bolt.adapter.socket_mode import SocketModeHandler
from dotenv import load_dotenv
from chatbot_engine import chat, create_index
from langchain.memory import ChatMessageHistory

load_dotenv()

index = create_index()

# ボットトークンとソケットモードハンドラーを使ってアプリを初期化します
app = App(token=os.environ.get("SLACK_BOT_TOKEN"))


def fetch_history(channel: str) -> ChatMessageHistory:
    # AIのユーザーIDを取得
    bot_user_id = app.client.auth_test()["user_id"]

    conversations_history = app.client.conversations_history(channel=channel, limit=3)

    history = ChatMessageHistory()

    # 会話の流れを正確にするためにreversedで配列を逆にする
    for messages in reversed(conversations_history["messages"]):
        text = messages["text"]

        if messages["user"] == bot_user_id:
            history.add_ai_message(text)
        else:
            history.add_user_message(text)
        
    return history


@app.event("app_mention")
def handle_mention(event, say):
    channel = event["channel"]
    history = fetch_history(channel)

    message = event["text"]
    bot_message = chat(message, history, index)
    say(bot_message)


# アプリを起動します
if __name__ == "__main__":
    app_env = os.environ.get("APP_ENV", "production")

    if app_env == "production":
        # productionの場合、待ち受けモードで起動
        app.start(port=int(os.environ.get("PORT", 3000)))
    else:
        # production以外の場合、SocketModeで起動
        SocketModeHandler(app, os.environ["SLACK_APP_TOKEN"]).start()
```

## RenderのEnvironmentの変更 

https://dashboard.render.com/web/srv-cn8mpgi1hbls73dbssag/env

SLACK_SIGNING_SECRET=xxxx
SLACK_BOT_TOKEN=xxxx

を設定。以下Slackの値を参照。

### SLACK_SIGNING_SECRET

https://api.slack.com/apps/A06LXSDHM9N/general?

の App Credentials → Signing Secret の値を設定

### SLACK_BOT_TOKEN

https://api.slack.com/apps/A06LXSDHM9N/oauth?

の Advanced token security via token rotation → Bot User OAuth Tokenの値を設定

## RenderのSettingsを変更

https://dashboard.render.com/web/srv-cn8mpgi1hbls73dbssag/settings

### Start Command

Start Commandをslack_app.pyに変更

```bash
poetry run python -u src/slack_app.py
```

## SlackのSocket ModeのOFF

https://app.slack.com/app-settings/T06LXS8FXMW/A06LXSDHM9N/socket-mode 

の Enable Socket Mode をOFF 

## SlackのEvent Subscriptions のRequest URLを設定

https://api.slack.com/apps/A06LXSDHM9N/event-subscriptions?

Request URLにRenderでデプロイしたアプリのURLを設定 
（後ろに/slack/eventsを追加）

例）
https://lanbchain-001.onrender.com/slack/events

Save Changesを押下して設定を保存する