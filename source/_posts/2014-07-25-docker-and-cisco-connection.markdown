---
layout: post
title: "docker と CiscoVpnConnection の相性が悪くて辛みがあった件の対処"
description: "docker_and_cisco_connection"
category: "docker"
tags: ["docker"]
---

## TOC
* 
{:toc}

## 起きていたこと
docker info とか docker ps とかしても  

{% blockquote %}
Post http://192.168.59.103:2375/v1.13/containers/create: dial tcp 192.168.59.103:2375: operation timed out
{% endblockquote %}
  
とか出まくってマジ辛い感じになった。


[これ](https://www.google.co.jp/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0CB4QFjAA&url=https%3A%2F%2Fbotbot.me%2Ffreenode%2Fdocker%2F2014-07-21%2F%3Ftz%3DAmerica%2FLos_Angeles&ei=zjfSU9nvO9bp8AXYloL4CA&usg=AFQjCNFIh-SOKA95uGKggfe5WGwUVf2NTA&sig2=3_Zk6QOd_8nSzsp0S4lW5g&bvm=bv.71667212,d.dGc)とかみると definetly routing problem て感じだった。  

とりあえずしんどかったので、いつもの port fowarding で対応。  

## 解法
  
{% blockquote %}
ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -o LogLevel=quiet -p 2022 -i /Users/vimtaku/.ssh/id_boot2docker docker@localhost -L2375:localhost:2375
{% endblockquote %}
  
docker ps, docker info できた。  

以上。

