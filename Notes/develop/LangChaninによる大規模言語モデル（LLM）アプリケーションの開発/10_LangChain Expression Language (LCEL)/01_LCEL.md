## サンプル１

プロンプトとLLMをチェインさせる例

```python
from langchain.chat_models import ChatOpenAI
from langchain.prompts import PromptTemplate
from dotenv import load_dotenv

load_dotenv()

# PromptTemplate
prompt = PromptTemplate.from_template("""料理のレシピを教えてください。

料理名: {dish}""")

# LLM
llm = ChatOpenAI(model_name="gpt-3.5-turbo", temperature=0)

# Chain
chain = prompt | llm

# Result
result = chain.invoke({"dish": "カレー"})
print(result.content)
```

## サンプル２

AIの返答をもとに独自クラスを作成する例

```python
from langchain.chat_models import ChatOpenAI
from langchain.output_parsers import PydanticOutputParser
from langchain.prompts import PromptTemplate
from pydantic import BaseModel, Field
from dotenv import load_dotenv

load_dotenv()

# OutputParser
class Recipe(BaseModel):
  ingredients: list[str] = Field(description="ingredients of the dish")
  steps: list[str] = Field(description="steps to make the dish")

output_parser = PydanticOutputParser(pydantic_object=Recipe)

# PromptTemplate
prompt = PromptTemplate.from_template("""料理のレシピを教えてください。

{format_instructions}

料理名: {dish}""", partial_variables={"format_instructions": output_parser.get_format_instructions()})

# LLM
llm = ChatOpenAI(model_name="gpt-3.5-turbo", temperature=0)

# Chain
chain = prompt | llm | output_parser

# Result
result = chain.invoke({"dish": "カレー"})
print(type(result))
print(result)
```
