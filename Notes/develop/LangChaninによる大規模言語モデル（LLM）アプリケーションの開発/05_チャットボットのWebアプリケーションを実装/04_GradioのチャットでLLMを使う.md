## 必要なパッケージをインストール

```bash
pip install langchain==0.1.6
pip install openai==1.12.0
pip install langchain-openai==0.0.6
```

## 実装

```bash
touch src/chatbot_engine.py
touch src/gradio_ai_chatbot.py
```

## src/chatbot_engine.py 

```python
from langchain.globals import set_verbose
from langchain_openai import ChatOpenAI
from langchain.memory import ConversationBufferMemory

set_verbose(True)

def chat(message: str) -> str:
    llm = ChatOpenAI(model_name="gpt-3.5-turbo", temperature=0)
    result = llm.invoke(message)
    return result.content
```

## src/gradio_ai_chatbot.py

```python
import gradio as gr
from dotenv import load_dotenv
from chatbot_engine import chat

def ai_response(message, history):
    return chat(message)

demo = gr.ChatInterface(ai_response)

if __name__ == "__main__":
  load_dotenv()
  demo.launch()
```