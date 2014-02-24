---
layout: post
title: "factory_girl_active_support_concern"
description: ""
category: "ぼやき"
tags: ["ruby"]
---

## ぼやき
rspec でテストを書いていて、ある(concern)モジュールを include した active record なクラスで、  
どうしても、ActiveSupport::Concern を include したところに インスタンスメソッドを定義しても  
NoMethodError(undefined method) になってしまう..  

