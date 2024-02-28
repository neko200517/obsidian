https://www.kagoya.jp/howto/it-glossary/develop/github_ssh/
## 秘密鍵・公開鍵を作成する

```bash
ssh-keygen -t rsa
```

##  公開鍵の内容をコピーする

```bash
cat {ホーム}/.ssh/id_rsa.pub
```

## GitHubアカウントの設定でSSHを設定する

https://github.com/settings/keys

にアクセスし、New SSH Key を押下する。以下の内容を設定する。

Title | Key Type | Key 
| -- | -- | -- |
| 任意の名前 | Authentication Key | 先ほどコピーした公開鍵の値 |

## Gitをクローンする

```bash
cd {プロジェクトの場所}
git clone git@github.com:neko200517/langchain-001.git
```