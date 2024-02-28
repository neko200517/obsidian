## Agetns

Agentsを使うとLLMが様々なツールを選択して使用しながら動作する。

https://python.langchain.com/docs/modules/agents/

### ツールの例

- Bash
- Google Search
- Python
- Wikipedia API
- etc...

## サンプル

Windowsでは動作不可

```python
from langchain.agents import AgentType, initialize_agent
from langchain_openai import ChatOpenAI
from langchain.tools import ShellTool
from dotenv import load_dotenv

load_dotenv()

llm = ChatOpenAI(temperature=0)

shell_tool = ShellTool()
self_ask_with_search = initialize_agent(
    [shell_tool], llm, agent=AgentType.CHAT_ZERO_SHOT_REACT_DESCRIPTION, verbose=True
)

result = self_ask_with_search.run("What is your current directory?")

print(result)
```