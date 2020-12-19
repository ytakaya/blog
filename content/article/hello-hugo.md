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

などが考えられます。この中でも静的サイトジェネレーターを利用するメリットは、ブログサービスなどと比較して高速である、自分でカスタマイズ可能な範囲が広い、CMSと比較してサーバーが必要ない、などが上げられます。速度については画像の重さなどにも影響を受けるため一概に速いとは言えないようです[^1]、今度検証してみたい。

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
hugo serveをしてテーマが適用されているか確認してみます。
```
$ hugo serve
```
http://localhost:1313 にアクセスすると、結果が確認できます。

{{< figure src="/images/hello-hugo-1.png" title="hugo serveの結果" class="center" width="320" height="320" >}}

まだ記事を作成していないので、タイトルしか表示されていません。

## 記事を書いてみる
ついに記事を書いてみます。hugo newコマンドで記事の雛形を作成できます。
```
$ hugo new article/sample.md
```
すると、`/content`配下に`article/sample.md`というファイルが生成されます。  
生成されたファイルを編集し、記事を公開します。公開する際は`draft`を`false`にします。  
完成した`sample.md`がこちら。
```
---
title: "Sample"
date: 2020-12-18T17:34:26+09:00
draft: true
---

こんにちは、初めての記事です。
```
hugo serveで結果を確認します。
ホーム画面に先ほど書いた記事のリンクが見つかります。

{{< figure src="/images/hello-hugo-2.png" class="center" width="320" height="360" >}}

リンクをクリックすると記事が正常に反映されている事を確認できます。

{{< figure src="/images/hello-hugo-3.png" class="center" width="320" height="360" >}}

記事は一般的なmarkdown記法で書くことができます。

## Github Pagesに公開
最後にGithub Pagesを使ってブログを公開します。[公式ドキュメント](https://gohugo.io/hosting-and-deployment/hosting-on-github/)を参考にしました。
### リポジトリを作る
まずはじめにGithub上にブログ用のリポジトリを作ります。私は`blog`というリポジトリにしました。  
次にリポジトリの`Setting`の`Github Pages`という項目で`docs/`を選択して設定を保存します。  
そうすると、https://<ユーザー名>.github.io/<リポジトリ名>/ にアクセスできるようになります。
### 設定ファイルの編集
公開用に`config.toml`に以下の設定を追加します。
```
baseURL = "https://ytakaya.github.io/blog/" # 公開するGithub Page: https://<ユーザー名>.github.io/<リポジトリ名>/
publishDir = "docs" # Github Pagesで公開するディレクトリの指定
```
### 書いた記事をGithub Pagesへ反映
最後に記事をGithub Pagesへ反映するには、`hugo`コマンドを実行して記事をビルドしてから、先ほど設定したリポジトリに変更をpushします。この一連の処理を記述したスクリプトファイルを作っておくと、記事の更新が楽になります。  
記事更新スクリプトの名前を`deploy.sh`とします。`chmod`で実行権限を付けることを忘れずに。
```
#!/bin/sh

# Build the project.
hugo

# Add changes to git.
git add .

# Commit changes.
msg="rebuilding site $(date)"
if [ -n "$*" ]; then
	msg="$*"
fi
git commit -m "$msg"

# Push source and build repos.
git push origin main
```
このスクリプトを実行すると、Github Pages上に記事が反映されていることを確認できます。
```
$ ./deploy.sh "commit message"
```

## まとめ
HugoとGithub Pagesを使ってブログを公開する方法をまとめました。テンプレートファイルをいじって自分なりにブログをカスタマイズしていきたいです。

[^1]: https://beacats.com/is_static_site_faster/