## gradio_app.py

```python
import gradio as gr
from dotenv import load_dotenv
from chatbot_engine_old import chat, create_index
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

## chatbot_engine.py 

```python
from langchain.globals import set_verbose
from langchain_openai import ChatOpenAI, OpenAIEmbeddings
from langchain.memory import ChatMessageHistory
from langchain_community.document_loaders import DirectoryLoader
from langchain.indexes import VectorstoreIndexCreator
from langchain.indexes.vectorstore import VectorStoreIndexWrapper

from langchain.memory import ConversationBufferMemory
from langchain.agents import tool
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
from langchain.agents.format_scratchpad.openai_tools import (
    format_to_openai_tool_messages,
)
from langchain.agents.output_parsers.openai_tools import OpenAIToolsAgentOutputParser
from langchain.agents import AgentExecutor

set_verbose(True)

def create_index() -> VectorStoreIndexWrapper:
    loader = DirectoryLoader("./src/", glob="**/*.py")
    return VectorstoreIndexCreator(embedding=OpenAIEmbeddings()).from_loaders([loader])


@tool
def get_index_query(word: str) -> str:
    """Retrun Python source code"""
    index = create_index()
    return index.query(question=word)


tools = [get_index_query]


def chat(message: str, history: ChatMessageHistory, index: VectorStoreIndexWrapper) -> str:
    llm = ChatOpenAI(model_name="gpt-3.5-turbo", temperature=0.9)

    system_template = """
    あなたはユーザーの質問に臨機応変に答えるエンジニアの女性アシスタントです。
    あなたはプログラミングサポートの他に日常的な会話に答えることが可能です。
    """

    prompt = ChatPromptTemplate.from_messages(
        [
            (
                "system",
                system_template,
            ),
            MessagesPlaceholder(variable_name="chat_history"), # userより上に配置する
            ("user", "{input}"),
            MessagesPlaceholder(variable_name="agent_scratchpad"),
        ]
    )

    llm_with_tools = llm.bind_tools(tools)

    agent = (
        {
            "input": lambda x: x["input"],
            "agent_scratchpad": lambda x: format_to_openai_tool_messages(
                x["intermediate_steps"]
            ),
            "chat_history": lambda x: x["chat_history"],
        }
        | prompt
        | llm_with_tools
        | OpenAIToolsAgentOutputParser()
    )

    memory = ConversationBufferMemory(
        chat_memory=history, memory_key="chat_history", return_messages=True
    )

    agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True, memory=memory)

    result = agent_executor.invoke({"input": message, "chat_history": history})

    return result["output"]
```
