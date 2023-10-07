## WLS2 のセットアップ

以下の記事を参考にWSL2をWindowsにインストールするための初期設定を済ませておく。

https://learn.microsoft.com/ja-jp/windows/wsl/install
https://qiita.com/whim0321/items/ed76b490daaec152dc69
https://zenn.dev/fehde/articles/83e542fda97e2a

### 設定を有効化する

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

### Ubuntuのセットアップ（初回起動時のユーザー作成）

初回起動時のユーザー作成

```shell
# 新しく作成するユーザー名を入力
Enter new UNIX unsername: tjg22983

# パスワードを入力
New password: P@ssw0rd

# パスワードを再入力
Retype new password: P@ssw0rd
```

### Ubuntuのセットアップ（その他、言語設定など）

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
