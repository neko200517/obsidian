## 機械学習用のフレームワーク

- Streamlit ... データ分析用のダッシュボードを簡単に実装
- Gradio ... 機械学習モデルを使ったWebアプリをより簡単に実装可能

## Gradioのインストール

```bash
poetry add gradio@4.19.1
```

### 注意

後にRender.comにデプロイするためにダウングレードする

```
poetry add gradio@3.48.0
```

## Quick Start

https://www.gradio.app/guides/quickstart/

```bash
touch src/gradio_app.py
```

```python
import gradio as gr

def greet(name, intensity):
    return "Hello, " + name + "!" * int(intensity)

demo = gr.Interface(
    fn=greet,
    inputs=["text", "slider"],
    outputs=["text"],
)

demo.launch()
```

## 実行

```bash
poetry run python src/gradio_app.py
```

## ホットリロード

```bash
poetry run gradio src/gradio_app.py
```