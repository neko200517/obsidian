src/*.pyの内容を読み込んで答えられるように実装する

## 必要なパッケージをインストール

```bash
poetry add unstructured@0.12.4
poetry add chromadb@0.4.22
```

### 注意

後にRender.comにデプロイするためにダウングレードする

```
poetry add chromadb@0.3.29
```

## src/gradio_app.py

```python
import gradio as gr
from dotenv import load_dotenv
from chatbot_engine import chat
from chatbot_engine import create_index
from langchain.memory import ChatMessageHistory

import os

def ai_response(message, chat_history):
    history = ChatMessageHistory()
    for [user_message, ai_message] in chat_history:
       history.add_user_message(user_message)
       history.add_ai_message(ai_message)

    bot_message = chat(message, history, index)
    
    return bot_message

demo = gr.ChatInterface(ai_response)

if __name__ == "__main__":
  load_dotenv()

  app_env = os.environ.get("APP_ENV", "production")

  # 開発環境がproductionの場合のみ認証がかかる
  if app_env == "production":
     username = os.environ["GRADIO_USERNAME"]
     password = os.environ["GRADIO_PASSWORD"]
     auth = (username, password)
  else:
     auth = None

  index = create_index()

  demo.launch(auth=auth)
```

## src/chatbot_engine.py

```python
from langchain.globals import set_verbose
from langchain_openai import ChatOpenAI
from langchain.memory import ChatMessageHistory
from langchain_community.document_loaders import DirectoryLoader
from langchain.indexes import VectorstoreIndexCreator
from langchain.indexes.vectorstore import VectorStoreIndexWrapper
from langchain_openai import OpenAIEmbeddings

set_verbose(True)

def create_index() -> VectorStoreIndexWrapper:
    loader = DirectoryLoader("./src/", glob="**/*.py")
    return VectorstoreIndexCreator(embedding=OpenAIEmbeddings()).from_loaders([loader])
    

def chat(message: str, history: ChatMessageHistory, index: VectorStoreIndexWrapper) -> str:
    llm = ChatOpenAI(model_name="gpt-3.5-turbo", temperature=0)

    return index.query(question=message, llm=llm)
```