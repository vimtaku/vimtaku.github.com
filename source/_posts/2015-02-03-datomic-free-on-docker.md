---
layout: post
title: "boot2docker 内に立てた datomic transactor + peer のコンテナに対して、mac の lein repl で datomic を操作する"
description: "boot2docker 内に立てた datomic transactor + peer のコンテナに対して、mac の lein repl で datomic を操作する"
category: "datomic"
tags: ["datomic", "docker"]
---

## boot2docker 内に立てた datomic transactor + peer のコンテナに対して、mac の lein repl で datomic を操作する

### 下準備(docker image の作成)
[ここに Datomic の Dockerfile があった](https://registry.hub.docker.com/u/colinrymer/docker-datomic-free/dockerfile/)  
ので、datomic の version は違うが修正したら、使えるだろうと思ってたら、使えた。  
  
最終的な Dockerfile は [こちら](https://gist.github.com/vimtaku/94cef31a166921b9b7f2)。
これを元に build する。  

```
    docker build -t datomicfree .
```

```
    docker exec -ti {container_id} /bin/bash
```
などとして、デバッグしたりした。  

### 実行環境の説明
MacbookPro -> boot2docker(vagrant) -> docker container  
という構成になっている。  

### docker container 起動
最初、 boot2docker の vagrant の割り当てメモリが小さすぎて、 datomic が立たなかったので、  
一度 vm を停止してから、割り当てを変更して

```
    docker run -p 4334:4334 -p 4335:4335 -p 4336:4336 -m 512m -t-i datomicfree
```
などとすることで、うまく起動した。  
結局、 port foward は 4334 だけで良いと思ったがそれだけではダメなので注意。  

```
    ssh -L 4334:localhost:4334 -L 4335:localhost:4335 -L 4336:localhost:4336 docker@localhost -p 2022
    (password: tcuser)
```

### lein repl
あとは repl から操作するだけ。  

```
    (def uri "datomic:free://localhost:4334/test")
    (d/create-database uri)
    (def conn (d/connect uri))
```
あとは先ほどの Dockerfile のリンクにもあるので、そちらを参照のこと。

## 所感
ちょっとハマったけど、これができたのは大きい。  

