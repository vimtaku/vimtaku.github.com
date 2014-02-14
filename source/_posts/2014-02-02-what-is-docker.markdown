---
layout: post
title: "Docker の概要をしらべて触ってみた"
description: "Docker の概要をしらべて触ってみた"
category: "docker"
tags: [docker]
keywords: "docker"
---

{% include JB/setup %}

## TOC
* 
{:toc}

## はじめに
この記事では Docker について自分なりに理解するために調べたことをまとめる。  
Docker の tutorial は command line を ブラウザで実際に叩くところまでできるようになっているので、  
非常にわかりやすく入門できる。なので、 tutorial に関してはそっちをやったほうが良い。  
この記事は Docker ってこんな感じのやつなんだーってのが伝わればばいいなと思う。  
(ちなみに まだ Docker 歴は1日)

## docker とは
LXC と aufs と github のようなものをうまくくみ合わせて提供される  
仮想化ソフトウェアだ。  

## LXC とは
Linux Container のこと。  

[http://gihyo.jp/admin/column/01/vm/2011/lxc_container](http://gihyo.jp/admin/column/01/vm/2011/lxc_container)によれば、  
{% blockquote %}
LXCの基本技術となるのが「コンテナ」と呼ばれる一種のリソース管理システムです。ファイルシステムの他，ホスト名やプロセス，ネットワークソケットなどのカーネルが扱うさまざまなリソースの管理テーブルを個々に用意し，これをコンテナごとに管理することで，コンテナごとに独立したOSのように動作させることができます。
{% endblockquote %}
とある。  
また、カーネルの機能である [Cgroups](http://ja.wikipedia.org/wiki/Cgroups) の説明をみれば非常によく理解できた。  

## aufs, Union FS とは
ここに、 Union mount と Union type file system の資料がある。  
非常にわかりやすい説明だった。ここに、 aufs の説明もある。結構古いけど。。  
[http://www.oreilly.co.jp/community/blog/2010/02/union-mount-uniontype-fs-part-1.html](http://www.oreilly.co.jp/community/blog/2010/02/union-mount-uniontype-fs-part-1.html)
ちょっとだけ簡単に自分用にまとめる。  

### union mount
filesystem A と filesystem B をマウントして、それぞれが file A, file B を同じディレクトリで持っていたとすると、  
union mount をつかったとき、fileA, file B の両方のファイルが見える。  

### 読み込み
open システムコールとかを通して呼ぶと、 union 機能が上から順に読んでいけば、差分的に一番新しいものを読める。  
両方のファイルシステムが fileA を持っていて、その内容がちがう場合、filesystem の mount の優先度(レイヤー順?)で  
どちらの filesystem の fileA が見えるか変わるんだと思う。  

### 書き込み
なんらかを書き込んで差分ができたとき、 上位ディスクとは違うものを保証しなければならないため、  
上位ディスクのものをコピーして書き込む。だからコピーオンライト。  
でもこの場合は、 copy-up と呼ばれて、プロセス fork でいうそれとは区別されている模様。  

### 削除
ファイルが削除されても、上位ディスクには存在しているため、事実上削除ができない。  
なので 特別なファイルを作成することで 消えたように見せる。(DB で言う論理削除みたいなもんだと認識)  
whiteout という。  

## github 的な要素
[index.docker.io](https://index.docker.io) レポジトリに image を push できる。  
memcached を [Dockerfile によって image を作る tutorial](https://www.docker.io/learn/dockerfile/) があるので、それをやった。  
それでできた Dockerfile  を使って repository に push した例を以下に示す。  
ちなみに、 [https://index.docker.io/account/](https://index.docker.io/account/) にあらかじめ登録しておいた。  

{% highlight bash %}
vagrant@ubuntu-12:~$ docker login
Username: vimtaku
Password:
Email: ******@gmail.com
Login Succeeded

vagrant@ubuntu-12:~$ docker build -t vimtaku/memcached_sample - < Dockerfile

vagrant@ubuntu-12:~$ docker images
REPOSITORY                 TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
vimtaku/memcached_sample   latest              242bd405025e        22 seconds ago      217.1 MB
run_memcached              latest              5f1cda03f61c        17 hours ago        217.1 MB
memcached                  latest              9166d484dda8        17 hours ago        217.1 MB
brand_new_memcached        latest              7bdd82d0abf7        20 hours ago        217.1 MB
memcached_new              latest              c18168a45363        20 hours ago        245.5 MB
ubuntu                     12.10               426130da57f7        7 days ago          127.6 MB
ubuntu                     quantal             426130da57f7        7 days ago          127.6 MB
ubuntu                     10.04               8589d4e9c7c6        7 days ago          139.6 MB
ubuntu                     lucid               8589d4e9c7c6        7 days ago          139.6 MB
ubuntu                     12.04               72e10143e54a        7 days ago          125.9 MB
ubuntu                     latest              72e10143e54a        7 days ago          125.9 MB
ubuntu                     precise             72e10143e54a        7 days ago          125.9 MB
ubuntu                     13.10               721f07d19f96        7 days ago          144.6 MB
ubuntu                     saucy               721f07d19f96        7 days ago          144.6 MB
ubuntu                     13.04               476aa49de636        7 days ago          133.6 MB
ubuntu                     raring              476aa49de636        7 days ago          133.6 MB

vagrant@ubuntu-12:~$ docker push vimtaku/memcached_sample
The push refers to a repository [vimtaku/memcached_sample] (len: 1)
Sending image list
Pushing repository vimtaku/memcached_sample (1 tags)
511136ea3c5a: Image already pushed, skipping 
b74728ce6435: Image already pushed, skipping 
72e10143e54a: Image already pushed, skipping 
28d8e9cef54f: Image successfully pushed 
6be17bb13216: Image successfully pushed 
1b910f3ee9b7: Image successfully pushed 
18e04c08eb9b: Image successfully pushed 
2e32ba041afa: Image successfully pushed 
1cf353d00dd8: Image successfully pushed 
242bd405025e: Image successfully pushed 
Pushing tags for rev [242bd405025e] on {https://registry-1.docker.io/v1/repositories/vimtaku/memcached_sample/tags/latest}
{% endhighlight %}

<br />

### 出来上がりイメージ
<img src="http://gyazo.com/6a99a7bdffa6ce80369dad852461aacb.png" style="width:100%;"/>

## 所感
この記事では、 docker の一番の利点だと感じている、 image を簡単に作って試す、みたいな部分は書いていない。  
すごい早さで image を作り出すことができるのには感動した。そこが一番の利点だと思う。  
あと、[この oreilly の資料](http://www.oreilly.co.jp/community/blog/2010/02/union-mount-uniontype-fs-part-1.html)は 3回くらい読む価値はありそうだ。  

## 参考文献
[http://ja.wikipedia.org/wiki/LXC](http://ja.wikipedia.org/wiki/LXC)  
[http://gihyo.jp/admin/column/01/vm/2011/lxc_container](http://gihyo.jp/admin/column/01/vm/2011/lxc_container)  
[http://ja.wikipedia.org/wiki/Cgroups](http://ja.wikipedia.org/wiki/Cgroups)  
[http://www.oreilly.co.jp/community/blog/2010/02/union-mount-uniontype-fs-part-1.html](http://www.oreilly.co.jp/community/blog/2010/02/union-mount-uniontype-fs-part-1.html)  
[http://teppeis.hatenablog.com/entry/docker](http://teppeis.hatenablog.com/entry/docker)  
[http://shibayu36.hatenablog.com/entry/2013/12/30/173949](http://shibayu36.hatenablog.com/entry/2013/12/30/173949)  
[http://2013.8-p.info/japanese/06-22-docker.html](http://2013.8-p.info/japanese/06-22-docker.html)  

