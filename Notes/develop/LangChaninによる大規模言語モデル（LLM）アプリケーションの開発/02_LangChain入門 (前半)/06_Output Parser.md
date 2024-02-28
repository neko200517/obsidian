## Output Parser 

出力をstring型だけでなく他の形式にすることも可能。

## サンプル

https://python.langchain.com/docs/modules/model_io/output_parsers/quick_start

```python
from langchain.output_parsers import PydanticOutputParser
from langchain.prompts import PromptTemplate
from langchain_openai import ChatOpenAI
from langchain_core.pydantic_v1 import BaseModel, Field, validator
from typing import List
from dotenv import load_dotenv

load_dotenv()

model = ChatOpenAI(model_name="gpt-3.5-turbo", temperature=0)

class Recipe(BaseModel):
  ingredients: List[str] = Field(description="ingredients of the dish")
  steps: List[str] = Field(description="steps to make the dish")

templete = """
料理のレシピを教えてください。

{format_instructions}

料理名: {dish}
"""

parser = PydanticOutputParser(pydantic_object=Recipe)

prompt = PromptTemplate(
    template=templete,
    input_variables=["dish"],
    partial_variables={"format_instructions": parser.get_format_instructions()},
)

chain = prompt | model | parser
result = chain.invoke({"dish": "カレー"})

print(result)
```