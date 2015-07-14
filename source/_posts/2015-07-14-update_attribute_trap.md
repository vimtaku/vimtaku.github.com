---
layout: post
title: "model.update_attribute の罠"
description: "model.update_attribute の罠"
category: "ruby"
tags: ["ruby", "rails", ""]
---


## 背景
update_attribute で一つだけupdate しようと思ってたら他の属性も変わってて意味不明だった。  

## ヌワー

```
user = User.first
p user.name  ## "vimtaku"

user.name = "hoge"
user.update_attribute(:age, 100)
##  UPDATE users SET `name` = 'hoge', `age` = 100 where `id` = 1

```

結局 user.reload した。
