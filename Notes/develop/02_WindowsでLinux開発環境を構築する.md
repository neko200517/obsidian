## WLS2 のセットアップ

https://learn.microsoft.com/ja-jp/windows/wsl/install
https://qiita.com/whim0321/items/ed76b490daaec152dc69
https://zenn.dev/fehde/articles/83e542fda97e2a
### 設定を有効化

「Windowsの機能の有効化、または無効化」画面で「Linux用Windowsサブシステム」と「仮想マシンプラットフォーム」が有効になっていることを確認する。
### WSL2 をインストール

管理者モードでPowerShellを起動して、以下を実行

```Powershell
# インストール可能なバージョンを確認 
wsl --list --online

# インストール
wsl --install Ubuntu-22.04
```

デフォルトでUbuntuがインストールされる。別のディストリビューションを指定する場合は以下のように実行する。

```PowerShell
wsl --install -d Debian
```

### デフォルトの WSL のバージョンを設定 

```PowerShell
wsl --set-default-version 2
```

## Ubuntu のセットアップ 

https://zenn.dev/ryuu/articles/wsl2-locale-jp

### 初期設定１

初回起動時のユーザー作成

```shell
# 新しく作成するユーザー名を入力
Enter new UNIX unsername: tjg22983

# パスワードを入力
New password: P@ssw0rd

# パスワードを再入力
Retype new password: P@ssw0rd
```

### 初期設定２

その他、言語設定など

```shell
# アップデート 
sudo apt update && sudo apt upgrade 

# ロケールの確認
locale -a

# 日本語の言語パックをインストール 
sudo apt install -y language-pack-ja

# 日本語ロケールを設定 
sudo update-locate LANG=ja_JP.UTF8

# タイムゾーンの変更
sudo dpkg-reconfigure tzdata

# コマンドのマニュアル表示も日本語化する
sudo apt -y install manpages-ja manpages-ja-dev
```

## Docker のインストール

https://zenn.dev/dynamitecoolguy/articles/7c7c2ca06553e5

### 手順

#### GPGキー追加

```shell
# GPCパッケージを追加
$ sudo mkdir -p /etc/apt/keyrings
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

#### aptリポジトリに追加 

```shell
# aptリポジトリに追加
$ echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu jammy stable" | sudo tee /etc/apt/sources.list.d/docker.list
```

#### Docker のインストール

```shell
# Dockerのインストール
$ sudo apt update
$ sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

#### インストールの確認 

```shell
# インストールの確認 
docker -v
docker-compose -v
```

#### dockerコマンドをsudo無しでも使用できるようにする

```shell
$ sudo usermod -aG docker $USER
$ newgrp docker
```

#### containerdのconfigのversionを2にする

※ 以下は環境によって実施

```shell
sudo vim /etc/containerd/config.toml
```

```toml: /etc/containerd/config.toml
# /etc/containerd/config.toml
...(略)...
version = 2                                           ← 追加

disabled_plugins = ["io.containerd.grpc.v1.cri"]      ← 追加
#disabled_plugins = ["cri"]                           ← コメントアウト("#"追加)
...(略)...
```

#### dockerのIP設定変更

※ 以下は環境によって実施

```shell
sudo nano /etc/docker/daemon.json
```

```json
// 下記IPアドレスは各自の環境によって変更
{
    "bip": "192.168.96.254/24",
    "fixed-cidr": "192.96.128.0/24"
}
```

```shell
$ sudo systemctl restart docker
```
