## AWS Lambdaにデプロイ

### python のインストール

Ubuntuはpythonがシステムと依存関係となっているため可能な限り排他的にpythonをインストールする。

#### pyenvのインストール

```sh
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
```

#### pyenvへパスを通す

```sh
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc echo 'eval "$(pyenv init -)"' >> ~/.bashrc
```

#### .bashrcを再読み込み

.bashrc の変更内容を即実行可能にする。

```sh
source ~/.bashrc
```

#### python をインストール

my-appディレクトリ直下で実行する。

```sh
pyenv install 3.9.18
```

#### プロジェクトのディレクトリへ移動し、使用するpythonを指定する

```sh
cd my-app

# カレントディレリに使用するバージョンをマウント
pyenv local 3.9.18

# 確認する
python --version

# マウントの解除
pyenv local --unset
```

### Nodejsとnpmの導入 

#### Ubuntu20.24 LTSにnodejsとnpmをインストール 

```sh
sudo apt update & apt install nodejs npm
```

#### 最新化する

Ubuntuのパッケージマネージャからインストールできるnodejsとnpmは古いため最新化する。

##### 参考サイト
- [Ubuntu に最新のNode.jsをインストールしてみた](https://makandat.wordpress.com/2017/11/08/ubuntu-%E3%81%AB%E6%9C%80%E6%96%B0%E3%81%AEnode-js%E3%82%92%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB%E3%81%97%E3%81%9F%E3%80%82/)
- [n (Node.js管理) のインストール手順](https://qiita.com/tora_oba/items/2761452fe8c2b0035ba0)

```sh
# n の導入
sudo npm cache clean
sudo npm install n -g

# 最新化してシンボリックリンクを更新
sudo n stable
sudo ln -sf /usr/local/bin/node /usr/bin/node

# 古いnodejsとnpmを削除
sudo apt purge -y nodejs npm 
sudo apt autoremove -y

# バージョン確認
node -v
npm -v

# LTSのバージョン確認 
n --lts

# バージョン一覧
n --all lsr

# 他のバージョンをインストールする
sudo n lts    # LTSバージョン
sudo n latest # 最新
sudo n xx.xx  # バージョン指定

# 他のバージョンに切り替える 
n
```

### Serverless の導入

#### Serverless のインストール

あらかじめ nodejs のインストールを済ませておくこと。

```sh
npm i serverless
```

#### Serverless の初期化

```sh
npx serverless
```

#### プラグインのインストール 

```sh
sls plugin install -n serverless-python-requirements
```

### ファイル構成

#### 参考例

```sh
my-app
├── .serverless
├── src
│   └── main.py
├── .gitignore
├── README.md
├── requirements.txt
└── serverless.yml
```

### 仮想環境の構築

#### 仮想環境の作成

```shell
# 仮想環境の作成
python -m venv venv

# アクティブ化
source ./venv/bin/activate
```

#### 必要なパッケージのインストール 

```shell
pip install fastapi 
pip install uvicorn
pip install mangum
```

#### requirements.txt を出力

serverless-python-requirements は requirements.txt のパッケージをインストールしてzipに固めてデプロイしてくれるので以下のコマンドでリストを出力する。

==新しいパッケージをインストールしたらデプロイ前に実行すること==

```shell
pip freeze > requirements.txt
```

### APIの作成 

#### FastAPI で API を作成 

src/main.pyに以下のプログラムを作成。

```python
# src/main.py
from fastapi import FastAPI
from mangum import Mangum

app = FastAPI()

@app.get("/")
async def index():
    return {"message": "Hello world!"}

handler = Mangum(app)
```

#### serverless.yml ファイルの編集

```yml
# serverless.yml

service: aws-python-http-api-project
frameworkVersion: '3'

# pythonデプロイ用のプラグイン
plugins:
  - serverless-python-requirements

provider:
  name: aws
  runtime: python3.9
  region: ap-northeast-1

# src/**以外のファイルをデプロイしないようにする
package:
  patterns:
    - '!**'
    - 'src/**'

custom:
  defaultStage: dev
  pythonRequirements:
    dockerizePip: true
    layer: true        # パッケージをレイヤー化する
    slim: true         # __pycache__などを除外してくれる

functions:
  service:
    handler: src.main.handler
    events:
      - http:
          path: /
          method: ANY
      - http:
          path: /{path+}
          method: ANY
    layers:
      - Ref: PythonRequirementsLambdaLayer

```

### デプロイ 

#### デプロイを実行

あらかじめaws cliのインストールとクレデンシャルの設定を済ませておくこと。

==Windows環境だとdockerが入ってない場合デプロイできないため、[[02_01_WindowsでLinux開発環境を構築する]] を参考にLinux環境でデプロイすること。==

```shell
npx sls deploy --stage dev
```

### 参考サイト

#### Serverless Python Requirements
https://www.serverless.com/plugins/serverless-python-requirements 

#### FastAPI と Mangum で作るサーバーレスAPI
https://zenn.dev/hayata_yamamoto/articles/781efca1687272

#### python,AWS-FastAPIで作るサーバーレスAPI(Mangum)
==ECRにデプロイしたDocker ImageでServerlessのデプロイをするTips==
https://zenn.dev/mini_hiori/articles/mangum-serverless

#### AWS LambdaでNumpy、Scipyを使ってみる！Serverless Frameworkとプラグインを使えばパッケージ管理が簡単！
==NumpyやScipyなど大きサイズのパッケージをデプロイする手法==
https://dev.classmethod.jp/articles/serverless-framework-lambda-numpy-scipy/

#### serverless.ymlを一行一行、見ていくしかないんや
==serverless.yml の解説==
https://zenn.dev/han_aru/articles/aabac861bc519d1220a5