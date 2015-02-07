---
layout: post
title: "さくらvps に CoreOS を入れる"
description: "さくらvps に CoreOS を入れる"
category: "vps"
tags: [vps, docker]
---

# さくらvps に CoreOS を入れる
基本的には
[http://qiita.com/yujiod/items/dc154120c4df2e938111](http://qiita.com/yujiod/items/dc154120c4df2e938111)
に従ってやれば行ける。  

ただ、自分は VirtIO を ON にしたせいか、  
ネットワークの設定のところで、

```
    [Match]
    Name=ens3
```

となっているところの ens3 は eth0 にしないと繋がらなかった。  
(cloud-config も同様)

# 所感
英字キーボードを久しぶりに打ったけど慣れてないのでめちゃくちゃ辛かった。  
何もはまらなければすぐできるとは思うけど、  
普通にダウンロードとか調査とか考えたら1時間くらいかかると思う。  
