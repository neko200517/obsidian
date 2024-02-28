Chainはモジュール（Models, Templates, Chainsなど）を連結する

## サンプル

```python
import langchain
from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI
from dotenv import load_dotenv

load_dotenv()

langchain.verbose = True

template = """
次のコマンドの概要を説明してください。

コマンド: {command}
"""

prompt = ChatPromptTemplate.from_template(template)
model = ChatOpenAI(model_name="gpt-3.5-turbo", temperature=0)
output_parser = StrOutputParser()

chain = prompt | model | output_parser

result = chain.invoke({"command": "ls"})

print(result)

```