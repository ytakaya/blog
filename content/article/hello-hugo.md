---
title: "Hugo+Github Pagesを使ってブログを公開する"
date: 2020-12-18T11:18:05+09:00
draft: false
tags: ["hugo"]
---

HugoとGithub Pagesを使ってブログを公開したので、その備忘録です。

## Hugoとは
[Hugo](https://gohugo.io/)とはGo製の静的サイトジェネレーターです。静的サイトジェネレーターとは、事前に用意したテンプレートとコンテンツから静的なサイトを生成してくれるツールです。有名な静的サイトジェネレーターには、Gatsby、Next、Nuxt、Hugoなどが上げられます。[こちらのサイト](https://jamstack.org/generators/)に様々な静的サイトジェネレータがGithubのスター数などと一緒に見ることができます。  
ブログ公開の方法には、

- Qiitaやはてなブログなどのブログサービスを利用する方法
- WordPressなどのCMSを利用する方法
- 静的サイトジェネレーターを利用する方法  

などが考えられます。この中でも静的サイトジェネレーターを利用するメリットは、ブログサービスなどと比較して高速である、自分でカスタマイズ可能な範囲が広い、CMSと比較してサーバーが必要ない、などが上げられます。速度については画像の重さなどにも影響を受けるためブレがあるそうです[^1]、今度検証してみたい。

## Hugoのインストールとブログの作成
### インストール
環境は以下の通り
- macOS Catalina 10.15.6

macでは`brew`を使ってインストールできます。
```
$ brew install hugo
```

### ブログの作成
先ほどインストールしたHugoを使ってブログを作成します。
```
$ hugo new site sample-site
```
このコマンドを実行すると`sample-site`というディレクトリが生成され、その中にブログの雛形が入っています。
ここで、git initしておきましょう。
```
$ cd sample-site
$ git init
```

### テーマの適用
次にブログに適用するテーマを選定します。Hugoで使えるテーマは[このサイト](https://themes.gohugo.io/)に列挙されています。ここから自分の好きなテーマを選んで`/themes`配下にgit submoduleを使ってcloneします。git submoduleとは、特定のリポジトリを現在のリポジトリ内の特定のディレクトリに紐付ける仕組みです。もちろん自分で0からテーマを作り上げることもできます。  
今回はanankeというテーマを使います。適用するテーマを決めたら、
```
$ git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
```
実行するコマンドは決めたテーマのオフィシャルページを参照してください。
自分でテーマを作る際は[このドキュメント](https://gohugo.io/templates/)を参照して作っていくのが良さそうです。既存テーマをちょっといじりたい際にも参考になります。  
最後に設定ファイルの`config.toml`を編集してブログにテーマを適用します。
```
baseURL = "https://example.com" # ブログの公開URL、あとで編集する
languageCode = "ja" # 言語設定
title = "my blog" # ブログのタイトル
theme = "ananke" # テーマ名。自分で選定したテーマ名にする
```


## Github Pagesに公開


[^1]: https://beacats.com/is_static_site_faster/