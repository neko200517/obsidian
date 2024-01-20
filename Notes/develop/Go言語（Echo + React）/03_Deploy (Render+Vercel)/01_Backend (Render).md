## DBインスタンスを作成

1. https://dashboard.render.com/ にアクセス
2. New PostgreSQL を選択
3. 以下の設定でインスタンスを作成 
	1. Name: DB
	2. Region: Singapore (Southeast Asia)
	3. Instance Type: Free 
	4. ほかはデフォルト
	5. Create Databaseを押下
4. StatusがAvailableになるまで待機

## 接続情報をコピー

Connections
- Hostname: xxxxx
- Port: xxxxx
- Database: xxxxx
- Username: xxxx
- Password: xxxxx

## Web Serviceを作成 

1. New→Web Service を選択 
2. Build and deploy from a Git repository を選択してNext
3. (Repositoryに紐づいていなかった場合) Connect Repositoryを選択 → Install RenderでGithubにプラグインをインストール
4. 作成したGo/EchoリポジトリをConnectで選択 
5. 以下の設定でWebServiceを作成 
	1. Name: api
	2. Region: Singapore (Southeast Asia)
	3. Build Command: go build -tags netgo -ldflags '-s -w' -o app && go run migrate/migrate.go 
	4. Instance Type: Free 
	5. Environment Variables: 
		1. PORT: 8080
		2. POSTGRES_USER: コピーしたUsername
		3. POSTGRES_PASSWORD: コピーしたPassword
		4. POSTGRES_DB: コピーしたDatabase
		5. POSTGRES_PORT: コピーしたPort
		6. POSTGRES_HOST: コピーしたHostname
		7. SECRET: Generateボタンを押下
		8. GO_ENV: production
		9. API_DOMAIN: localhost （後で変更する）
		10. FE_URL: http://localhost:3000 （後で変更する）
	6. Advancedを展開
		1.  Auto-Deploy: No
	7. ほかはデフォルト 
	8. Create Web Serviceを押下 
	9. デプロイが失敗した場合Manual Deploy→Deploy latest commitを選択 
		1. 2024-01-08:
			1. go v1.21.5に対応していなかったため go.mod の内容を go 1.21.xx → go 1.21.4 に変更
6. StatusがLiveになったらデプロイ成功

## APIのURLをコピー 

Dashboard→api→左上のURLをコピー
