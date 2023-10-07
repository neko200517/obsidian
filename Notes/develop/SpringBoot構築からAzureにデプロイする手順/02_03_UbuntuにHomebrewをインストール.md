## PowerShellでデフォルトユーザーを変更する

WSLだと毎回OSにログインするたびにパスを登録し直さないといけないので、以下のコマンドを実行する。

ユーザー名をubuntuとした場合の例）

```powershell
ubuntu config --default-user ubuntu
```

## インストールに必要なものをインストール

```sh
sudo apt-get install build-essential curl file git
```

## Homebrewをインストールする

https://brew.sh/

```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

## パスを通す

```sh
test -d ~/.linuxbrew && eval $(~/.linuxbrew/bin/brew shellenv)
test -d /home/linuxbrew/.linuxbrew && eval $(/home/linuxbrew/.linuxbrew/bin/brew shellenv)
test -r ~/.bash_profile && echo "eval \$($(brew --prefix)/bin/brew shellenv)" >>~/.bash_profile
echo "eval \$($(brew --prefix)/bin/brew shellenv)" >>~/.profile
```

## シェルを再起動

```sh
exec $SHELL -l
```

## 使い方

### インストール 

```sh
brew install xxx
```

### アンインストール

```sh
brew uninstall xxx
```

### 検索

```sh
brew search xxx
```

### インストールしたものを表示

```sh
brew list
```

### brew経由でインストールしたサービスを起動する

```
brew service start xxxx
```
