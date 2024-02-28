## Vector Store の活用

文書をベクトル化してVector Storeに保存しておき、入力と近いベクトルの文書をVector Storeから検索してcontextに含めるという手法がある。

File Directory
https://python.langchain.com/docs/modules/data_connection/document_loaders/file_directory

インデックス作成
https://python.langchain.com/docs/modules/data_connection/indexing

## 必要なパッケージのインストール

```bash
pip install unstructured[md]
```

## サンプル

langchainの公式ドキュメントをベクトル化し要約するサンプル

```bash
git clone https://github.com/langchain-ai/langchain.git
```

```python
from langchain_community.document_loaders import DirectoryLoader
from langchain.indexes import VectorstoreIndexCreator
from langchain_openai import OpenAIEmbeddings, ChatOpenAI
from dotenv import load_dotenv

load_dotenv()

model = ChatOpenAI(model_name="gpt-3.5-turbo", temperature=0)

loader = DirectoryLoader('./langchain/docs', glob="**/*.md", use_multithreading=True)
index = VectorstoreIndexCreator(embedding=OpenAIEmbeddings()).from_loaders([loader])

query = "LangChainの概要を10代の少女が話している口調で説明してください。"
result = index.query(query, llm=model)

print(result)
```

## 出力

```txt
LangChainってすごいんだよ！これはさまざまな外部リソースと連携できるんだ。例えば、ファイルシステムやAPI、データベースなどね。だから、開発者はパワフルなLLMsの力を使って、外部リソースにアクセスしたり、相互作用したり、操作したりすることができるんだ。

でもね、このアプリを作るときは、セキュリティに気をつけなきゃいけないんだよ。悪い人たちが侵入してくるのを防ぐためにね。だから、アプリの必要な権限だけを設定することが大切なんだ。広範囲な権限を与えると、セキュリティの脆弱性が生じる可能性があるからね。機密情報にアクセスできないようにするために、読み取り専用の認証情報を使ったり、セキュリティ対策のテクニックを使ったりするといいんだよ。

だから、LangChainはすごく便利なんだけど、セキュリティには気をつけなきゃいけないんだよ！
```