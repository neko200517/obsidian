## フロントエンドをデプロイする

1. https://vercel.com/ にアクセス
2. Add New→Projectを押下
3. 連携しているGithubのリポジトリをImport
4. 以下の設定をしてDeploy 
	1. Project Name: react-todo
	2. Framework Preset: Vite
	3. Environment Variables: VITE_APP_API_URL: コピーしたRender.com APIのURL
	4. Addボタンを押下 
	5. ほかはデフォルト
	6. Deployボタンを押下
5. Conguratulations画面が表示されたら成功
6. Continue to Dashboardを押下
7. DomainsにURLが記載されているのでコピー

## バックエンドの環境変数を編集

1. FE_URL: VercelでコピーしたURL （末尾の/は削除）
	1. 例）https://xxxxx.app/ → https://xxxxx.app
2.  API_DOMAIN: Renderにデプロイしたapiのエンドポイントのhttps://を削除したアドレス 
	1. 例）https://api-xxxxx.com → api-xxxxx.com 
3. Save Changesを押下 
4. Manual Deploy→Deploy latest commitを押下 

## 完了

すべて完了したらVercelにデプロイしたアプリにアクセスして問題なく使用できるか確認する。
URL: https://react-todo-two-flax.vercel.app/
