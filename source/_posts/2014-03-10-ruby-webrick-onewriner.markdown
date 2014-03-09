---
layout: post
title: "Ruby の WEBRick のワンライナーでサーバが立つかすぐ確認する"
description: "Ruby の WEBRick のワンライナーでサーバが立つかすぐ確認する"
category: "ruby"
tags: ["ruby", "WEBRick"]
---

## ワンライナー
{% highlight ruby %}
ruby -e "require 'webrick'; server = WEBrick::HTTPServer.new( {:BindAddress => '0.0.0.0', :Port => 80}); trap(:INT){server.shutdown}; server.start;"
{% endhighlight %}

## 普通の方
{% highlight ruby %}
require 'webrick'
server = WEBrick::HTTPServer.new({:BindAddress => '0.0.0.0', :Port => 80})
trap(:INT){server.shutdown}
server.start
{% endhighlight %}

