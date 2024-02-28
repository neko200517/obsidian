## Render.com

1. https://dashboard.render.com にアクセス
2. 「New +」ボタン→ Web Serviceを押下
3. GitHubのリポジトリを選択しConnectを選択 

## 設定

Name: langchain-xxx
Reginon: US East
Branch: main
Root Directory:
Runtime: Python3
Build Command: poetry install
Start Command: poetry run -u python src/gradio_app.py
Instance Type: Free
Environment Variables:
	PYTHON_VERSION: 3.11.3
	GRADIO_SERVER_NAME: 0.0.0.0 
	PORT: 7860 
	GRADIO_USERNAME: xxxx 
	GRADIO_PASSWORD: xxxx 
	OPENAI_API_KEY: xxx

## サンプルアプリ

https://lanbchain-001.onrender.com