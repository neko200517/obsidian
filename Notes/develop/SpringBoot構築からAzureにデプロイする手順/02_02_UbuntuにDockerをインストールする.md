## Docker の最速インストール （最も簡単）

https://qiita.com/nujust/items/d7cd395baa0c5dc94fc5

### インストール 

ダウンロードしたスクリプトを実行するだけでインストール完了。

```sh
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

### ルート以外のユーザーでdockerコマンドを利用する

sudoなしでdockerを操作する。

```sh
sudo usermod -aG docker $USER
```

### systemdの有効化

WSLデフォルトだとサービスが有効化できないためサービス管理を可能にする。

```sh
sudo vi /etc/wsl.conf
```

```conf
# /etc/wsl.conf

[boot]
systemd=true
```

### WSLの再起動

これまでの操作を有効化する。

```powershell
wsl --shutdown
```

### Dockerの実行確認

```sh
docker run hello-world
```

### docker-compoesのインストール

```bash
sudo curl -L https://github.com/docker/compose/releases/download/v2.24.5/docker-compo
se-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

### docker-composeがインストールされているかを確認

```bash
docker-compose -v
```

### Dockerサービスを立ち上げる

```bash
sudo service docker start
```

## Docker のインストール（手動）

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
