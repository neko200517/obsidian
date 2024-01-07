## ユーザーを作成する

http://localhost:8000/api/create にアクセスし、任意のユーザーを作成する

## トークンを取得する

Postmanなどで以下のリクエストを実行する。

POST http://localhost:8000/api/auth

```json
{
	"username": "<作成したユーザー名>",
	"password": "<作成したパスワード>"
}
```

取得したトークンをコピーしておく。

## トークンをリクエストヘッダに付与して認証が必要なページにアクセスする

ChromeのModHeaderなどにトークンを付与して認証が必要なページにアクセスする。

例）

```header
Authorization = token xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

## テスト

以下の画面にアクセスし、CRUD操作が行えるかテストする。

http://localhost:8000/api/profile
http://localhost:8000/api/segments
http://localhost:8000/api/brands
http://localhost:8000/api/vehicles
