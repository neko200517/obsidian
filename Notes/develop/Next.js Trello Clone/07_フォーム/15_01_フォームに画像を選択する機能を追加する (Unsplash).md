## Unsplash Developers 

Unsplash の画像がAPIで取得可能

https://unsplash.com/developers

## Unsplash Developers にアクセスして、アプリケーションを作成する

Your apps ボタンを押下する

![[Pasted image 20240804144156.png]]

## New Application を選択してアプリケーションを作成する

New Application を押下する

![[Pasted image 20240804144309.png]]

## 利用規約をすべてのチェックする

すべてチェックして Accept terms を押下する

![[Pasted image 20240804144344.png]]

## アプリケーションの情報を入力する

例）
- Application name
	- trello-clone
- Description
	- trello-clone

Create application を押下する

![[Pasted image 20240804144550.png]]

## API Key

詳細画面に遷移するので、Api Key をコピーする

![[Pasted image 20240804145237.png]]

## .envファイルにアクセスキーをコピーする

### .env

```ts
//...
NEXT_PUBLIC_UNSPLASH_ACCESS_KEY=xxxxxxxx
```