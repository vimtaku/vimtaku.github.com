---
layout: post
title: "I wanna test view *_path on my rails console(pry), so what should i do?"
date: 2015-11-08 13:04:14 +0900
comments: true
categories: [rails]
---

## Just do

```
pry(main)> include Rails.application.routes.url_helpers

pry(main)> admin_customer_path(User.first)
User Load (0.3ms)  SELECT  `users`.* FROM `users`  ORDER BY `users`.`id` ASC LIMIT 1
=> "/admin/customers/1"
```

enjoy!
