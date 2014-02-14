---
layout: post
title: "jekyll plugin が使えない"
description: ""
category: "jekyll"
tags: [jekyll]
---
{% include JB/setup %}

## もくじ
* toc
{:toc}


## jekyll のプラグイン入れたら github pages がエラーでてビルドできなくなる件の対処

[こちらのブログ](http://gosyujin.github.io/2013/05/21/jekyll-plugin-githubpages/)
や
[github公式](https://help.github.com/articles/pages-don-t-build-unable-to-run-jekyll)
を見てもらえればわかるけど、unsafe な plugin を入れられない模様。

だから、結論としては静的 html を吐き出す感じにした。

## もともと master ブランチで運用していた人へのアドバイスと、実際に変更
約1分でできるからご安心を。

{% highlight bash %}
##  ソースブランチをコミット。以降はソースブランチで記事を書く
git checkout -b source
git push origin source:source

## _deploy を作ってそれを master として push する
mkdir _deploy
cd _deploy

## 適当に最初のコミットを作る、この辺はもっとかっこいい方法あると思うけど気にしない
git init
touch hoge
## 自分のいままでの master を remote add
git remote add origin git@github.com:vimtaku/vimtaku.github.com.git
git add .
git ci . -m 'Initial commit'
git push -f origin master:master

## _site を生成
cd ..
jekyll serve  --watch -P 4000 -H 127.0.0.1 --trace

## vim で Rakefile を修正
## これ(ひとのやつ)を参考に. https://github.com/gosyujin/gosyujin.github.com/commit/2943985064ced913767157eb0fdae431b68ac491
vim Rakefile

rake deploy

{% endhighlight %}

## ちなみに
ソース見た感じ一日で複数ポストした場合には、ポストの並び順は辞書順っぽい。

