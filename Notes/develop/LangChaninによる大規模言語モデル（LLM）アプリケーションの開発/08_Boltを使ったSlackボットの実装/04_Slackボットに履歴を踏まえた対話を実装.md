## Slackアプリに設定を追加

https://api.slack.com/apps/A06LXSDHM9N/oauth?success=1

ScopesのAdd an OAush Scopeを押下
→channel:historyを選択
→アプリに権限を付与

## src/slack_app.py

### conversations.historyを使用

https://api.slack.com/methods/conversations.history

- channelを取得 
- AIのユーザーIDを取得 
- Slackのchannelに基づいた履歴を取得
- 履歴からAIとそれ以外のユーザーの会話を履歴に設定 
- 履歴を踏まえたLLMを実行

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
    SocketModeHandler(app, os.environ["SLACK_APP_TOKEN"]).start()
```
