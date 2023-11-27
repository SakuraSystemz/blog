---
layout: posts
title: "JekyllとGitHub pagesでブログを組む時にハマった事"
date: 2023-08-06
tags: 備忘録 薄い記事
---
## はじめに
自分は今まで はてなブログ を使っていましたが、ProtonMailで登録できなかった事に加えトラッカーが多いのでgithub.ioドメインで立ち上げました。その際に難儀したポイント3点をここに書いておきます。<br>

申し遅れました、実際の生活と密接な名義として`Rai01e(SakuraSystemz)`を名乗らせてもらってます。<br>

記事の流れとしてRubyとJekyllのインストールとセットアップ方法は省きます。<br>
ただテーマ毎に仕様が違うので、それを応用的に運用する為にも公式のドキュメントは読んで損は無いと思います。ベースは同じなので。<br>
[https://jekyllrb.com/docs/](https://jekyllrb.com/docs/)

## ハマった点(1) Jekyllテーマの依存関係

ローカルでテストする段階の話です。正確に書くと`更新の途絶えたテーマの依存gemが要求するRubyバージョン`で躓く事が多かったです。<br>
自分の場合だと、見覚えのある[dark-poole](https://jamstackthemes.dev/theme/dark-poole/)や[Midnight](https://jamstackthemes.dev/theme/jekyll-midnight/)のビルド中Ruby v2.x系を要求された(自分のバージョンはv3.0.6)ので更新頻度の高い`minimal_mistakes`に決めました。<br>

rbenvでバージョン管理を行えば解決しますが、2.x系は怖くて無理でした。<br>

## ハマった点(2) Jekyllキャッシュに気をつけよ

基本的にGitHub pagesのコンテンツは全て全世界公開なので、ブログ用ローカルリポジトリの`.jekyll-cache`を削除してからコミットすることを推奨します。このディレクトリには該当リポジトリへのフルパスが含まれているので、端末のユーザー名がGitHubのリポジトリ経由で外部に公開されてしまいます。[OPSEC](https://en.wikipedia.org/wiki/Operations_security)の観点から削除しましょう。<br>
![.jekyll-cache](https://raw.githubusercontent.com/SakuraSystemz/blog/main/IMG/2023-08-06/vo.2/img1.png)
`jekyll server --disable-disk-cache`また`jekyll build --disable-disk-cache`で永続的に無効化できます。<br>

## ハマった点(3) GitHub pagesとのタイムゾーンのズレ

このブログを公開する際一番悩まされたのがGitHub pagesで公開した記事が反映されなかった事です。GitHub側のビルドと反映に時間がかかる事は前提知識としてありましたが、あまりにも遅かったのでBuildログを見ると`記事の読み込み自体がスキップされていました`
```
Reading: _posts/2023-08-06-post.md
Skipping: _posts/2023-08-06-post.md has a future date

```
とりあえずChatGPTでデバッグした所、GitHub側のビルドシステムのタイムゾーンがUTCであった為、日本時間よりUTCの方が進みが遅く2023年8月6日付けの記事が公開できない事がわかりました。そのタイムゾーンから考えて未来の記事は公開できないということですね。しかし、先程示した記事が8月6日付けなのは変えようがないので、`_config.yml`に`future: true`を埋め込む事で強引に解決させました。どうもGitHub側のタイムゾーンを`_config.yml`側からこれ以外迂回することはできないようです。<br>

## 終わりに
親切感のないアバウトな記事ですが、自分が難儀した3つのポイントを掲載しておきました。Rubyを長らく触っていたのでこのブログを作るまでの作業時間はおそらく3時間ぐらいでした。殆どの時間をJekyllとGitHub pagesのビルドシステムの研究に割きました。<br>

ともあれこの記事をご覧になった誰かの役に立てば幸いです。
