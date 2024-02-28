## Models

ModelsはLangChainで使用する機械学習のモデルのことを指す
Modelsには以下の3種類がある

- LLMs: OpenAIのCompletionsAPI（text-davinchi-003）などの大規模言語モデル
- Chat Models : OpenAIのChatAPI（gpt-4やgpt-3.5-turbo）のためのモジュール
- Text Embeddig Models: テキストをベクトルかするモデル

## パッケージのインストール

※2024/02月最新バージョン

```bash
pip install langchain==0.1.6
pip install openai==1.12.0
pip install langchain-openai==0.0.6
```

## サンプル

モデルを指定して実行

```python
from langchain_openai import ChatOpenAI
from dotenv import load_dotenv

load_dotenv()

llm = ChatOpenAI(model_name="gpt-3.5-turbo", temperature=0.7)

result = llm.invoke("自己紹介してください。")

print(result)
```