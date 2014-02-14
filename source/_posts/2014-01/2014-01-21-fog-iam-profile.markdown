---
layout: post
title: "fog っていう gem で use_iam_profile があんまうまく行かない件"
description: ""
category: "aws"
tags: [aws, fog]
keywords: aws fog ruby
---

{% include JB/setup %}

## 現象
iam_profile を使うことで、 aws_access_key や aws_access_secret を具体的に指定しなくても、  
metadata な endpoint にアクセスして一時的な access_key と secret を取得できるようになっている(できたのは割と最近らしい)。  
それなら、iam を使いたいわけなんだけど、 fog v1.18.0 と v1.19.0 で試しても use_iam_profile がうまくいかない。  

呼び出しているクライアントライブラリは asset_sync v1.0.0 と elasticsearch v0.3.4 。  
どちらも aws_credential fetcher で credential を取得してるみたいだけど、それを使ってないようにみえた。  

## 対処
全部読んでないからわからないけど、俺の予想が合っていれば  
credential を撮ってきたものを使用していないので ここが怪しかった。  

lib/fog/core/service.rb
{% highlight bash %}
-options = options.merge(fetch_credentials(options))
+options = fetch_credentials(options).merge(options)
{% endhighlight %}

とりあえずもっと調べなきゃよくわからない。  
これがうまくいっていそうなら pull req だしてみよう。  
