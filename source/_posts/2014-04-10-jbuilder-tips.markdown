---
layout: post
title: "jbuilder のフラグメントキャッシュで、配列で書きたい場合とハッシュで書きたい場合のキャッシュを共通化したい"
description: "jbuilder_tips"
category: "ruby"
tags: ["ruby", "jbuilder"]
---

## 結論
views/book/_book.jbuilder  
{% highlight ruby %}
json.cache! book do
  _j = book.to_builder.target!
    JSON.parse(_j).each do |k,v|
        json.set! k, v
    end
end
{% endhighlight %}

controller で @tmpl[:book] が set されていると仮定  
views/book/show.jbuilder  
{% highlight ruby %}
json.book do
  json.partial! 'book/book', book: @tmpl[:book]
end
{% endhighlight %}

controller で @tmpl[:books] が set されていると仮定  
views/book/list.jbuilder  
{% highlight ruby %}
json.books @tmpl[:books], partial:'book/book', as: :book
{% endhighlight %}

## 背景
単体表示に  
{% highlight ruby %}
{
    book: {
        bookId: "moge"
    }
}
{% endhighlight %}
複数表示に  
{% highlight ruby %}
{
    books:[
    {
      bookId: "moge"
    },
    {
      bookId: "moge2"
    }
    ]
}
{% endhighlight %}
としたいみたいなやつがググっても全然出てこなかったので。


## 所感
まぁこうは普通しないわなぁ。

