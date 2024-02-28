## サンプル

複数の実行結果に基づいて返答するエージェント。
以下の例では東京と大阪の結果をネットで検索し、それぞれの結果を要約して返却している。

```bash
poetry add duckduckgo-search@4.5.0
```

```python
import langchain
import openai
from langchain.agents import AgentType, initialize_agent, load_tools
from langchain_openai import ChatOpenAI

from dotenv import load_dotenv

load_dotenv()

langchain.debug = True
langchain.verbose = True
openai.log = "info"

tools = load_tools(["ddg-search"])
llm = ChatOpenAI(model="gpt-3.5-turbo", temperature=0)
agent = initialize_agent(tools, llm, agent=AgentType.OPENAI_MULTI_FUNCTIONS)

result = agent.run(
  "東京と大阪の天気を教えてください。"
)

print(f"""=== 結果 ===
{result}
""")
```

### 出力

```
=== 結果 ===
東京の今日の天気は20℃で曇りで、明日は17℃で雨の可能性があります。一方、大阪市では今日は晴れが続き、明日は曇りがちで気温は14度前後になります。詳細な天気情報はウェザーニュースなどのサイトで確認できます。
```