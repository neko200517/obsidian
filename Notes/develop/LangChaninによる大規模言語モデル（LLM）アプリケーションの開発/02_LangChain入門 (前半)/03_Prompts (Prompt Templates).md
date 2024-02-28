## Prompts

Promptsはモデルへの入力を組み立てるためのモジュール
Promptsには大きく4つの要素がある

- Prompts Templates: プロンプトをテンプレート化する
- Chat Prompt Templates 
- Example Selectors
- Output Parsers

## サンプル

```python
from langchain_core.prompts import PromptTemplate
from langchain_openai import ChatOpenAI
from dotenv import load_dotenv

load_dotenv()

template = """
次のコマンドの概要を説明してください。

コマンド: {command}
"""

prompt = PromptTemplate(input_variables=["command"], template=template)

print(prompt.format(command="ls"))

```

### 出力

```
次のコマンドの概要を説明してください。

コマンド: ls
```