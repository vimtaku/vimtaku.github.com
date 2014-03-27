---
layout: post
title: "Jbuilder で 共通パラメータを付与したい時は layout を使うといいかも"
description: "Jbuilder で 共通パラメータを付与したい時は layout を使うといいかも"
category: "rails"
tags: ["jbuilder", "ruby", "rails"]
---

## 前提
  - rails4
  - jbuilder(1.5.3)

## 背景
jbuilder を使うと、 api のレスポンスをシンプルに定義できる。  
例えば、hoge_controller.rb#new の場合に  
app/views/hoge/new.jbuilder とかを用意しておいて、  
そこに template で返したい値などを書いて定義できる。  
しかし、共通で値を返したい場合どうするんだってことになって  
いろいろ考えた結果以下に落ち着いた。  

## 解法
layout を使う。  
  
controller にたとえば  
layout 'api/application.jbuilder' 
などと定義しておく。  

それで上記の例で言うと、 hoge_controller.rb#new では  
app/views/hoge/new.jbuilder が呼ばれるので、  
  
app/views/hoge/new.jbuilder
{% highlight ruby %}
json.from_hoge "from_hoge_param"
{% endhighlight %}
  
layouts/api/application.jbuilder  
{% highlight ruby %}
# common に値を付与
json.common "common_param_is_here"
# controller の @hoge を参照できる
json.hoge @hoge
# template の値を付与
JSON.parse(yield).each do |k,v|
  json.set! k, v
end
{% endhighlight %}
とかしておくと良いのかもしれない。  
結果は
{common:"common_param_is_here", fromHoge:"from_hoge_param", hoge:"hoge_from_controller"}
てな感じになる。  
  
ちなみに camelCase には公式の通り、 Jbuilder.key_format camelize: :lower でできる。

上記は、まだ実践してないけど手元で試してみた見たかんじこれで行けそう。  

## 所感
もっといい方法あれば教えて下さい。  

## 蛇足
stack over flow とかで書いてある render! を定義する方法や  
json.render JSON.parse(yield)  だとうまく行かなかった。  

