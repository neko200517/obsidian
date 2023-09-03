## 概要

AWS Lambda + API Gateway + ECR + StandaloneモードでNextJSをデプロイ、公開する。

## ファイル構成

```sh
my-app
├── app
│   └── 省略
├── cdk
│   ├── bin
│   │   └── cdk.ts        # cdk-stackに定義しているclass名に変更する
│   ├── lib
│   │   └── cdk-stack.ts
│   └── 省略
├── next.config.js        # 変更
├── Dockerfile            # 追加
├── .dockerignore         # 追加
├── entrypoint.sh         # 追加
└── 省略
```

## Next.jsのプロジェクトを作成

```sh
npx create-next-app
```

## Next.js Standalone の設定

./next.config.js を編集し、Next.js を standalone モードにする

```js
// ./next.config.js

// 省略 
module.exports = {
  output: 'standalone',
}
```

## Dockerfile の作成 

./Dockerfile を新規作成する（ついでに.dockerignoreも）。

```sh
touch Dockerfile 
touch .dockerignore
```

オリジナルのDockerfileは[こちら](https://github.com/tmokmss/nextjs-lambda-cdk/blob/main/Dockerfile)

```Dockerfile
FROM node:16 AS builder
WORKDIR /build
COPY package*.json ./
RUN npm ci
COPY . ./
RUN npm run build

# Use a common base image to reduce the cold start time
FROM amazon/aws-lambda-nodejs:16

# Install Lambda Web Adapter
COPY --from=public.ecr.aws/awsguru/aws-lambda-adapter:0.5.0 /lambda-adapter /opt/extensions/lambda-adapter
ENV PORT=3000

COPY --from=builder /build/next.config.js ./
COPY --from=builder /build/public ./public
COPY --from=builder /build/.next/static ./.next/static
COPY --from=builder /build/.next/standalone ./

# Changes due to the base image
# 変更：Lambdaでキャッシュするように修正
COPY entrypoint.sh ./
ENTRYPOINT ["sh"]
CMD ["entrypoint.sh"]
```

## .dockerignore を作成 

イメージ化するときに余計なものを対象にしないために ./.dockerignore を作成。

```.dockerignore
node_modules
.next
cdk
```

## AWS CDK の設定 

CDKプロジェクト作成にはaws-cdk@2を使用する。

```sh
# あらかじめインストール
npx aws-cdk@2 --version

mkdir cdk 
cd cdk
npx aws-cdk@2 init app --language typescript
npm install @aws-cdk/aws-apigatewayv2-integrations-alpha @aws-cdk/aws-apigatewayv2-alpha
```

## CDKスタックの設定

./cdk/cdk-stack.ts を編集する。
```code: DockerImageCode.fromImageAsset('../', {``` にはDockerfileの 相対パスを指定する。

```ts
// ./ckd/cdk-stack.ts

import * as cdk from 'aws-cdk-lib';
import { Construct } from 'constructs';
import { Duration } from 'aws-cdk-lib';
import { HttpLambdaIntegration } from '@aws-cdk/aws-apigatewayv2-integrations-alpha';
import { DockerImageCode, DockerImageFunction } from 'aws-cdk-lib/aws-lambda';
import { HttpApi } from '@aws-cdk/aws-apigatewayv2-alpha';
import { Platform } from 'aws-cdk-lib/aws-ecr-assets';

export class Frontend extends cdk.Stack {
  readonly endpoint: string;

  constructor(scope: Construct, id: string, props: cdk.StackProps) {
    super(scope, id);

    // Lambdaの定義
    const handler = new DockerImageFunction(this, 'Handler', {
      code: DockerImageCode.fromImageAsset('../', {
        platform: Platform.LINUX_AMD64,
        
      }),
      memorySize: 256,
      timeout: Duration.seconds(30),
    });

    // API GatewayIの定義
    new HttpApi(this, 'Api', {
      apiName: 'Frontend',
      defaultIntegration: new HttpLambdaIntegration('Integration', handler),
    });
  }
}
```

## デプロイ準備

cdkディレクトリ直下で実行。
```cdk bootstrap``` で必要なリソースをAWSにプロビジョニングする。
あらかじめ [[AWS CLI のインストールと初期設定]] を済ませておくこと。

```sh
npm run cdk bootstrap
```

## デプロイ 

```sh
npm run cdk deploy
```

## デプロイしたアプリの動作確認

API Gateway のエンドポイントにアクセスしてアプリが表示されるか確認する。

## Cloud Front で配信するには

1. Cloud Front ディストリビューションのオリジン設定で今回作成した API Gateway Endpoint を選択する
2. キャッシュポリシーを Caching Optimized に設定する
3. API Gateway をCloud Front からのみアクセス可能にする [^1]
4. Route53 と Certificate Manager で独自ドメインを設定する
5. Cloud Front に WAF を関連付けする
 
[^1]: https://blog.grasswake.me/tech-posts/1646529783

## 参考サイト

- [AWS Lambda Web AdapterでServerless Next.jsを実現する](https://synamon.hatenablog.com/entry/2023/07/18/080000)
- [Next.jsをLambda + API Gatewayでサーバーレス化する (standaloneモード)](https://tmokmss.hatenablog.com/entry/20221213/1670891305)
- [Next.js 13 の SSR Streaming を AWS Lambda Response Streaming で実装する方法](https://aws.amazon.com/jp/blogs/news/implementing-ssr-streaming-on-nextjs-with-aws-lambda-response-streaming/)
- [API GatewayとLambdaでNext.jsをAWSにデプロイする with Terraform](https://zenn.dev/yamakenji24/articles/deploy-nextjs-with-aws#%E3%81%AF%E3%81%98%E3%82%81%E3%81%AB)
