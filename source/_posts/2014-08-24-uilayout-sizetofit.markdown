---
layout: post
title: "UITableViewCell の中の UILabel の内容によって動的に Cell の高さを変える(not autolayout)"
description: "uilayout_sizetofit"
category: "ios"
tags: ["ios", "swift"]
---

## UITableViewCell の中の UILabel の内容によって動的に Cell の高さを変える(not autolayout)
1. 基本的には[こちらの記事にそっておこなった](http://qiita.com/kotaroito/items/8bd2f10833e07f7a5809) が、自分のやり方がわるいのか、 selected 時しかうまくいかなかった。  
2. [このstackoverflow によると](http://stackoverflow.com/questions/17823581/custom-cell-not-resizing-uilabels) sizeToFit は layoutSubviews に入れよ。とのこと。  
  
{% blockquote %}
override func layoutSubviews() {
    super.layoutSubviews()
        self.body.sizeToFit()
}
{% endblockquote %}

## その他の参考
[http://qiita.com/yuch_i/items/b4612fae110254c816f4](http://qiita.com/yuch_i/items/b4612fae110254c816f4)
