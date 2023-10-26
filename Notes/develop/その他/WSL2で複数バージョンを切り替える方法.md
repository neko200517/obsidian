Dockerで複数環境を切り替えるよりも手軽に検証環境の破棄と切り替えが可能。

## 手順

1. 環境をエクスポート
2. 環境をインポート
3. 既定のユーザーを変更

## 環境の確認

ターミナルを起動し、次のコマンドで存在する環境を確認する。

```powershell
wsl -l -v
```

## 環境のエクスポート

次のコマンドで環境をエクスポートする。
例）Ubuntuという環境をUbuntu_copy.tarにエクスポート。

```powershell
wsl --export Ubuntu Ubuntu_copy.tar
```

## 環境をインポート

次のコマンドで環境をインポートする。
以下のフォーマットでimportを実行する。

```wsl --import (WSL下に登録する環境名) (インストール先のパス) (インポートする環境.tar)```

例）Ubuntu_copyという名前でUbuntu_copy.tarを読み込み、wsl_manual_installディレクトリに環境ファイルを作成する。

```powershell
wsl --import Ubuntu_copy .\wsl_manual_install\ Ubuntu_copy.tar
```

新しい環境は```wsl -l --verbose```で確認できる。

## 環境を起動する

```powershell
wsl -d Ubuntu_copy
```

## 既定のユーザーを変更

上記コマンドではrootユーザーとしてログインされてしまうので既定のユーザーを変更する。

### 1. ログイン

```powershell
wsl -d Ubuntu_copy
```

### 2. wsl.confを編集

rootユーザーでログイン後、以下のコマンドを入力して設定ファイルを編集。
例）規定のユーザーがubuntuの場合

```sh
echo -e "[user]\ndefault=ubuntu" | sh -c 'cat - > /etc/wsl.conf'
```

/etc/wsl.conf に以下の内容が出力されていればOK。

```ini
# /etc/wsl.conf

[user]
default=(ユーザー名)
```

### 3. 再起動

```powershell
wsl --shutdown Ubuntu_copy
wsl -d Ubuntu_copy
```

ユーザーが切り替わっていたらOK。

## 参考URL

https://qiita.com/souyakuchan/items/9f95043cf9c4eda2e1cc
https://aadojo.alterbooth.com/entry/2023/02/14/092811
