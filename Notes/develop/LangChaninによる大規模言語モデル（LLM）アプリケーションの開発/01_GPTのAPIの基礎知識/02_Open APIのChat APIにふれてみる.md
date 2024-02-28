## ドキュメント

https://platform.openai.com/docs/api-reference/chat/create

## 環境構築

```bash
python -m venv venv
./venv/Scripts/Activate
```

## 必要なパッケージのインストール

```bash
pip install openai 
pip install python_dotenv
```

## 環境変数ファイルを作成し、OpenAI APIのシークレットキーを保存する

```bash
touch .env
```

### .env

```env
OPENAI_API_KEY={取得したOpenAI APIのシークレットキー}
```

## 試してみる

```python
# OpenAIのCompletions APIのサンプル

import os
from dotenv import load_dotenv
from openai import OpenAI
import json

load_dotenv()

client = OpenAI()

response = client.chat.completions.create(
  model="gpt-3.5-turbo",
  messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Knock knock."},
        {"role": "assistant", "content": "Who's there?"},
        {"role": "user", "content": "Orange."},
  ],
  temperature=0, # 低ければ低いほど同じ答えが返ってくる
  n=2 # 答える回数
)

print(json.dumps(json.loads(response.model_dump_json()), indent=2))
```
