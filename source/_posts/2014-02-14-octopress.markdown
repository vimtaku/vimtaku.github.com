---
layout: post
title: "jekyll から octopress に10 分で移行する"
description: "jekyll から octopress に10 分で移行する"
category: "octopress"
tags: ["jekyll", "octopress"]
---

## 背景
デザインが見づらいって言われたので辛くなったから、  
octopress にノリで移行してみた。  
当方、 jekyll-bootstrap を使っていて、 octopress も普通に jekyll 使っているので  
問題なく移行できると思ったら移行できた。  

## 手順
大体こんなかんじでいける。空気さえ読めれば行ける感じ。  

{% highlight bash %}
git clone git://github.com/imathis/octopress.git octopress
cd octopress/
bundle install
rake install
rake preview
rake -T
vim config.rb
rake preview
rake setup_github_pages

## Rakefile の L269 の git push を -f するようにした。すでにレポジトリがあったから。
vim Rakefile

rake deploy

cp -a $HOME/jekyll-bootstrap/_posts/* .

## JB setup を記事に埋め込んでいるのがあったのでそれをケア(互換性のため)
mkdir $HOME/octopress/source/includes/JB
cp -a $HOME/jekyll-bootstrap/_includes/JB/setup $HOME/octopress/source/includes/JB
{% endhighlight %}

## ちなみに
テーマはコチラを使わせてもらった。  
[http://zespia.tw/Octopress-Theme-Slash/](http://zespia.tw/Octopress-Theme-Slash/)  
how to install 通りにやれば適用できる。  

Octopress テーマ集みたいなのはコチラ。  
[http://opthemes.com/](http://opthemes.com/)  

## 参考資料

[http://joelmccracken.github.io/entries/octopress-is-pretty-sweet/](http://joelmccracken.github.io/entries/octopress-is-pretty-sweet/)

