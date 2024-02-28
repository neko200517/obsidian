## サンプル

https://www.gradio.app/guides/creating-a-chatbot-fast

```python
import gradio as gr
import random

def random_response(message, history):
    return random.choice(["Yes", "No"])

gr.ChatInterface(random_response).launch()
```