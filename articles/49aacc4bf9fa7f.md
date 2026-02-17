---
title: "【Juno開発】変数・定数を作った時の感想と苦労した点"
emoji: "😸"
type: "tech"
topics:
  - "java"
  - "個人開発"
  - "自作言語"
  - "プログラミング言語"
  - "juno"
published: true
published_at: "2025-12-29 17:55"
publication_name: "individual_blog"
---

# はじめに
みなさんどうも。rii_125です。
最近、Junoを開発しているのですがついに変数、定数の**基礎の基礎**を完成させました。今までは、見せかけの変数という感じでしたが型チェックのある程度効いて、少し使えるようになったので紹介したいと思います。
また、Junoの開発を絶賛大募集中ですのでdiscordサーバーから参加を申し込むか、issueからでも参加を受け付けています。

https://discord.gg/58cxdaVv

https://github.com/rii125/Juno/issues/8

```java
public static main() {
    print("Welcome to Juno world!");
}
```
:::message
**注意**
変数・定数を宣言してもメモリに保存したり、書き出したりするような機能はまだ実装していません。
おそらく、次回の記事で紹介します。（知らんけど...）
:::

# 変数宣言
こちらを見てもらったほうが早いかもしれません。
https://zenn.dev/link/comments/fab92fe4969286
変数宣言は`var 変数名 : 型;`の宣言が基本です。変数の代入には`変数名 = 値;`となります。まあ、基本中の基本です。この文法に従ってコーディングをしていきます。

## 問題１【変数宣言＆代入の実装】
まず、変数宣言の原型が完成する前までは
```java
public static main() {
    var a : int = 125;
    var b : string = "rii_125";
}
```

でのみ変数が使えた。けれど、
```java
public static main() {
    var errorVar : string; // ←ここでエラー（変数宣言のみだと値と＝が検出されないから）
    errorVar = "rii_125"; // ←ここが読まれない
}
```
ということが発生しました。

### 解決策
解決策として、=がない場合は宣言だけとしてとらえるような設計になりました。
if文でチェックして=がない時だけ宣言のみの変数だと認識させました。

## 問題２　【定数が宣言できない】
本来定数は、`final`を使用して宣言します。
```java
public static main() {
    final PI : float = 3.14; // 定数として定義されるはず
}
```
しかし、未知の文字として表示され**RuntimeException**[^1]が発生しています。
そりゃそうですよ。だって定義すらしてませんもの。

### 解決策
1. `TokenType.java`に`FINAL`を定義
2. `TypedVarDeclacrationNode.java`にfinalの型の定義の仕方をデフォルトコンストラクタに再設定
3. `Lexer.java`にfinalの字句解析のための列挙をswitchに定義
4. `Parser.java`にfinalの文法表現やエラーの設定などを行う

この手順を行うことで、無事に解決することができました。
ちなみにここは、copilotに２時間くらいかけて完成させました。思った以上に時間がかかりすぎていて定数の実装に時間がかかるとは思ってもいませんでしたね。特に継承するときに暗黙の`super();`やうまく継承できていなかったりして自分のコードの汚さにビックリ！思った以上にコードの解析に時間がかかってしまったので次回からの反省ですね。😥
https://github.com/rii125/Juno/blob/9635b68ffa94a53e46e6ad1278c4dd337d1e24ab/Juno/ast/TypedVarDeclarationNode.java#L11-L17
*上のコードの`super();`の実装に30分くらいかかってます...*

## 問題3　【floatが機能しない】
浮動型小数であるfloatが全く機能していないことに気づいて定数のコーディングの後試しにfloatで実験すると、未知の文字として`.`が定義されていませんでした。（この時の僕は、floatの実装を終えていたと勘違いしていました。😅）
```java
public static main() {
    final PI : float = 3.14; // RuntimeException : 未知の文字'.'
}
```

### 解決策
`Lexer.java`に数値（int型とfloat型）にのみ`.`があると小数だと認識するようにするかつ型チェックを`TypeChecker.java`に行ってもらうことで解決できました。
https://github.com/rii125/Juno/blob/9635b68ffa94a53e46e6ad1278c4dd337d1e24ab/Juno/parser/Lexer.java#L20-L43

## 良い点
- 型チェックを前より厳格に行うことができた
- 宣言のみの変数・定数の宣言を実装することができた

## 反省点
- もう少しきれいにコードを書きたい
- 継承関係をもう一度きれいに整理したい
- コードレビューをする人がいない（Claudeにでもやってもらうかな...）

# まとめ
今回は、基礎の基礎が完成したJunoの変数について開発した感想と問題点・解決策を記事にしてみました。自分の課題点が早めに見つかってよかったなと思いつつ、今後は改めて設計の仕方を見直したいと思います。
またこの記事からZennCLIを導入しました。かな～り快適に作業をすることができたので皆さんもやってみてはいかがでしょうか。

---

**Junoのリポジトリは下から↓**
https://github.com/rii125/Juno/tree/v1.0.0-alpha

[^1]:想定していない例外や本来使用されない文法をJunoでは`RuntimeException`を採用して意図的に例外を生み出しています。