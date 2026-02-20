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

### 解決策②: OSの古い認証情報を削除と更新
Macの方は、
https://qiita.com/sue-hc/items/ea3628ed2668b06ca38c
を参考にしてもらうとわかりやすいです。

今回は、windowsでやっていきます。
```
検索ボックス > 「資格情報マネージャー」と検索 > 資格情報マネージャーを開く
```

もし、さっきのような手順でうまく表示されない場合には
```
コントロールパネル > ユーザーアカウント > 資格情報マネージャー
```
の順で開くこともできます。

最初に、`git:https://github.com`を選択して削除してください。
次に
```
github > setting > Developer settings > Personal access tokens > Fine-grained をクリック
```
これで、トークンの作成が求められるので設定を行った後にトークンを発行してください。

:::message
**注意**
絶対に、トークンを外部に漏らすようなことはしないでください！
また、絶対にトークンは忘れないようにメモを取るなど大切に保管してください！
:::

あとは、
```bash
git push origin ブランチ名
```
を入力することでユーザー名とパスワードの認証が求められるので、
ユーザー名: githubのユーザー名
パスワード: 作成したトークン

これで、完了です。
ちなみに、[この記事](https://qiita.com/sue-hc/items/ea3628ed2668b06ca38c)ではSSHを推しています。
トークンの作成がめんどくさい方は、最初からSSHを使用することをお勧めします。