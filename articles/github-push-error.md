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
3. `git:https://github.com` を削除した場合はもう一度エラーが起きたgitコマンドを実行してあげると解決する。
4. `git:https://github.com` と `git:https://github.com/ユーザー名` の両方を削除すると、gitのダイアログが出てきてアカウントの連携が求められるのでgithubと同じ名前とメールアドレスを使用してサインインする。
5. サインインが成功したら、エラーが起きたgitコマンドを実行してあげると解決する。
6. それでも解決しない場合には、gitのインストールをし直す。もしくは、githubの設定を疑う。