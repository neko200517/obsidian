## Sequential Chain

ChainとChainを直列に連結する

https://langfuse.com/docs/integrations/langchain/python

## サンプル

```python
from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI
from dotenv import load_dotenv

load_dotenv()

model = ChatOpenAI(model_name="gpt-3.5-turbo", temperature=0)
output_parser = StrOutputParser()

cot_template = """
以下の質問に回答してください。

### 質問 ###
{question}
### 質問終了 ###

ステップバイステップで考えてみましょう。
"""
cot_prompt = ChatPromptTemplate.from_template(cot_template)
cot_chain = cot_prompt | model | output_parser

summarize_template = """
入力を結論だけ一言に要約してください。

### 入力 ###
{input}
### 入力終了 ###
"""
summarize_prompt = ChatPromptTemplate.from_template(summarize_template)
summarize_chain = summarize_prompt | model | output_parser

# Sequential Chain
sequential_chain = {"input": cot_chain} | summarize_chain | output_parser

question = """
私は市場に行って10個のりんごを買いました。
隣人に2つ、修理工に2つ渡しました。
それから5つのリンゴを買って1つ食べました。
残りは何個ですか？
"""

result = sequential_chain.invoke({"question": question})

print(result)

```