## python-dotenvのインストール

```bash
poetry add python-dotenv@1.0.1
```

## .envファイルの用意

```bash
echo "OPENAI_API_KEY={Open AIで取得したAPIキー}" >> .env
```

## 環境変数の読み込み

```python
import gradio as gr
import random

from dotenv import load_dotenv

def random_response(message, history):
    return random.choice(["Yes", "No"])

demo = gr.ChatInterface(random_response)

if __name__ == "__main__":
  load_dotenv()
  demo.launch()
```