## Scoop のセットアップ

Windowsでyumやaptのようなパッケージマネージャで環境構築できるので便利になる。

PowerShellで以下のコマンドを実行

```PowerShell
# Scoopをインストール
Set-ExecutionPolicy RemoteSigned -scope CurrentUser
iwr -useb get.scoop.sh | iex
```

バージョンを確認
```shell
# バージョンを確認 
scoop --version
```

## Scoop の基本的な使い方 

基本的な使い方

```shell
# リストをアップデート
scoop update

# ソフトをアップデート
scoop update git

# extras bucketを追加
scoop bucket add extras

# bucketを削除
scoop bucket rm extras

# 検索
scoop search git

# インストール 済のリスト
scoop list

# インストール 
scoop install git

# アンインストール
scoop uninstall git

# 実行ファイルの再設定 - ショートカットが消えた場合などの対応
scoop reset *

# バージョン指定 - 複数バージョンをインストールしている場合に、使用するバージョンを切り替え
scoop reset openjdk8-redhat
scoop reset openjdk11
```

## 必要なソフトをインストール 

### 開発環境

基本的な開発環境、エディタなど

```shell
# git
scoop install git

# java - インストールするバージョンはscoop searchで事前に検索する
scoop install openjdk11

# nodejs
scoop install nodejs-lts

# python
scoop install python

# vscode
scoop install vscode
```

### データベース

DBエンジン、クライントなど

```shell
# mysql
scoop install mysql 

# postgresql
scoop install postgresql

# dbeaver - SQLクライアント
scoop dbeaver
```

### AWS 関連

aws-cliやterraformなど

```shell
# aws-cli - AWSリソースのデプロイ、クレデンシャル情報の管理
scoop install aws

# terraform - インフラ構築
scoop install terraform
```

### Azure 関連 

azコマンド、azure-functionsのローカル環境構築など

```shell
# azure-cli - Azureのログインやデプロイに必須
scoop install azure-cli 

# azure-functions-tools - Azure Functionsのローカル開発環境に必須
scoop install azure-functions-core-tools
```

### Java 関連

プロジェクト作成、IDE、Android開発環境など

```shell
# gradle
scoop install gradle

# groovy
scoop install groovy

# maven
scoop install maven

# adb - android仮想デバイス作成
scoop install adb

# intellij-idea - JavaのIDE
scoop install intellij-idea 

# android-studio - Android開発用にカスタマイズされたIDE
scoop install android-studio
```

### SSH/FTP

```shell
# サーバにSSH接続 - 本当はRLoginが使いたいが公式リポジトリにないので除外
scoop install teraterm

# サーバにFTP接続 
scoop install winscp
```

### その他

その他便利なユーティリティなど

```shell
# jq - jsonを読みやすくする
scoop install jq

# 7zip - 圧縮・解凍
scoop install 7zip

# 検索エンジン
scoop install everything

# 日々のメモから進捗管理などドキュメントの管理に長けたエディタ
scoop install obsidian
```
