## Ubuntuにログインし、以下のインストールを行う

https://asdf-vm.com/guide/getting-started.html

### 必要なパッケージをインストール

```bash
sudo apt-get update && sudo apt dist-upgrade -y && sudo apt autoremove -y
sudo apt install -y curl git
```

### asdfをインストール 

```bash
git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.14.0
echo -e "\n. $HOME/.asdf/asdf.sh" >> ~/.bash_profile
echo -e '\n. $HOME/.asdf/completions/asdf.bash' >> ~/.bash_profile
source ~/.bashrc
```

## Ubuntuに依存関係をインストール 

https://github.com/pyenv/pyenv/wiki#suggested-build-environment

```bash
sudo apt update; sudo apt install build-essential libssl-dev zlib1g-dev \
libbz2-dev libreadline-dev libsqlite3-dev curl \
libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev
```

## asdfにpythonプラグインを追加する

```bash
asdf plugin-add python
```

### インストールできるpythonの確認

```bash
asdf list all python
```

## PythonとPoetryをインストールする

```bash
cd {インストールしたいプロジェクトの場所}
echo "python 3.11.3" >> .tool-versions
echo "poetry 1.1.14" >> .tool-versions
asdf install
```

### .tool-versions 

```
python 3.11.3
poetry 1.1.14
```

## 確認 

```bash
python --version
poetry --version
```
