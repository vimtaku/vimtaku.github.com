---
layout: post
title: "jekyll blockquote 修正"
description: ""
category: "jekyll"
tags: [jekyll]
---
{% include JB/setup %}

## jekyll で blockquote で改行とかいい感じにならない問題
なんか jekyll で 最初 markdown で > で引用できるって知らなくて  
やってみたら空白とか改行がいい感じに削られてイラッとしたので  
それっぽい plugin ないかと思って調べたら github にblockquote  
plugin があったのでそれを使ってみたら全く同じだったので  
fork して quote と 改行入れる風にした。

## 一応カスみたいな差分
[https://github.com/vimtaku/jekyll-plugins/commit/810d8505d7bcbe0a4575ce5a8e20a366b9ddc0f9](https://github.com/vimtaku/jekyll-plugins/commit/810d8505d7bcbe0a4575ce5a8e20a366b9ddc0f9)
gsub! 使った方がいいのでもういっこ差分足した

## toc について
table of contents 機能が地味にあって  

{% blockquote %}
* 
{:toc}
{% endblockquote %}
って感じで書くといいらしい。  


