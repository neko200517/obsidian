## ロールを明確にしたうえで実装 


```python
from langchain_openai import ChatOpenAI
from langchain.schema import AIMessage, HumanMessage, SystemMessage
from langchain.globals import set_verbose, set_debug
import openai
from dotenv import load_dotenv

openai.log = "debug"
set_debug(True)
set_verbose(True)

load_dotenv()

chat = ChatOpenAI(model_name="gpt-3.5-turbo", temperature=0, max_tokens=100)

messages = [
  SystemMessage(content="You are a helpful assistant."),
  HumanMessage(content="Hi I'm Jun.")
]

result = chat(messages)
print(result)
```

## Chat APIに特化した実装 

HumanとAIの役割を明確にした実装

```python
from langchain_openai import ChatOpenAI
from langchain.memory import ConversationBufferMemory
from langchain_openai import ChatOpenAI
from dotenv import load_dotenv

load_dotenv()

chat = ChatOpenAI(model_name="gpt-3.5-turbo", temperature=0, max_tokens=100)
memory = ConversationBufferMemory()

while True:
  user_message = input("You: ")
  memory.chat_memory.add_user_message(user_message)

  ai_message = chat(memory.chat_memory.messages)
  memory.chat_memory.add_ai_message(ai_message.content)
  print(f"AI: {ai_message.content}")
```