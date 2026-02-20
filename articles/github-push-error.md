---
title: "GitHubで出た \"403エラーの解決方法\""
emoji: "🖥️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["git", "github", "windows"]
published: false
publication_name: "individual_blog"
---

# 要約
1. Gitで `fatal: unable to access 'https://github.com/ユーザー名/リポジトリ.git/': The requested URL returned error: 403` というエラーが発生。
2. 僕の場合は、windowsの資格情報マネージャーから `git:https://github.com` と `git:https://github.com/ユーザー名` の削除。
3. `git:https://github.com` と `git:https://github.com/ユーザー名` の両方を削除すると、gitのダイアログが出てきてアカウントの連携が求められるのでgithubと同じ名前とメールアドレスを使用してサインインする。
4. サインインが成功したら、エラーが起きたgitコマンドを実行してあげると解決する。
5. それでも解決しない場合には、gitのインストールをし直す。もしくは、githubの設定を疑う。

## エラーが起きた
```bash
git push -u origin main
```
を実行しようとすると、`fatal: unable to access 'https://github.com/ユーザー名/リポジトリ.git/': The requested URL returned error: 403`が発生。
geminiに聞くと、

> ① 認証情報（PAT）が古い or 間違っている
> ② リポジトリにアクセス権がない（Private / 権限不足）
> ③ gitに登録している URL が正しくない or 別アカウントのリポジトリ

心当たりがあるのは、①と③。

### 解決策①: `git remote -v` で今のgithubの接続を確認
```bash
git remote -v
    origin  https://github.com/ユーザー名/リポジトリ名.git (fetch)
    origin  https://github.com/ユーザー名/リポジトリ名.git (push)
```
このように表示されていたら問題なし。
```bash
git remote -v
    origin  https://github.com/ユーザー名/リポジトリ名.git/ (fetch)
    origin  https://github.com/ユーザー名/リポジトリ名.git/ (push)
```
末尾に`/`が入っていると、gitが正しくリンクを読み込めない場合があり`fatal: unable to access 'https://github.com/ユーザー名/リポジトリ.git/': The requested URL returned error: 403`が出る。

これに、関して問題ない場合には①が原因の可能性が高い。