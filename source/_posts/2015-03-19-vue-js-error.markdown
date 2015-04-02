---
layout: post
title: "vue.js ではまったこと"
description: "vue.js ではまったこと"
category: "vuejs"
tags: [vuejs]
---

## TypeError: this.el.removeAttribute is not a function
とかいうエラーが出た。
いろいろ悩みまくったが、 x-template の直下に二つのdom を並べたらエラーになるっぽい。

