過去のやり取りを記録して返答に反映させる。

ConversationBufferMemory
ConversationChain
https://python.langchain.com/docs/modules/memory/types/buffer

## サンプル

```python
from langchain.memory import ConversationBufferMemory
from langchain_openai import ChatOpenAI
from langchain.chains import ConversationChain
from dotenv import load_dotenv

load_dotenv()

model = ChatOpenAI(model_name="gpt-3.5-turbo", temperature=0, max_tokens=100)

convesation = ConversationChain(llm=model, memory=ConversationBufferMemory(), verbose=True)

while True:
  user_message = input("You: ")
  ai_message = convesation.predict(input=user_message)
  print(f"AI: {ai_message}")
```