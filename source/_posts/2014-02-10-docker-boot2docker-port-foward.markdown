---
layout: post
title: "docker_boot2docker_port_foward"
description: "docker_boot2docker_port_foward"
category: "docker"
tags: [docker]
---

{% include JB/setup %}

## OS X 10.8 公式サポートで話題の Docker

[これ](http://qiita.com/kanekoa/items/cf3cabb23da69c609002)をもとにとりあえず docker client を準備,  
docker server を建てた。  

## mac の localhost に telnet 11211 したら memcached につながるようにしたい

[https://www.docker.io/learn/dockerfile/level2/](https://www.docker.io/learn/dockerfile/level2/)
これをもとに Dockerfile を作った。 ここでは ubuntu を元にした vimtaku/memcached_1 とした。  

Dockefile
{% highlight bash %}
 
# Memcached
#
# VERSION       2.2

# use the ubuntu base image provided by dotCloud
FROM ubuntu

MAINTAINER Victor Coisne victor.coisne@dotcloud.com

# make sure the package repository is up to date
RUN echo "deb http://archive.ubuntu.com/ubuntu precise main universe" > /etc/apt/sources.list
RUN apt-get update

# install memcached
RUN apt-get install -y memcached

# Launch memcached when launching the container
ENTRYPOINT ["memcached"]

# run memcached as the daemon user
USER daemon

# expose memcached port
EXPOSE 11211
{% endhighlight %}


{% highlight bash %}
docker build -t vimtaku/memcached_1 - < Dockerfile
{% endhighlight %}
これでとりあえず memcached な image を作れる。  

{% highlight bash %}
docker run -p 11211:11211 vimtaku/memcached_1
{% endhighlight %}
これで boot2docker -> ubuntu が立つ。  
  

## 追記(2014/02/13)
成功法っぽいのを発見した。  
恥ずかしながら ssh port fowarding が -L オプションで できるっていうのを
[このブログ記事](http://motemen.hatenablog.com/entry/2014/02/11/Dockerfile_%E3%82%92%E5%85%83%E3%81%AB%E3%82%B3%E3%83%B3%E3%83%86%E3%83%8A%E3%82%92%E8%B5%B0%E3%82%89%E3%81%9B%E3%81%A6%E3%83%AD%E3%83%BC%E3%82%AB%E3%83%AB%E3%81%AB%E3%83%9D%E3%83%BC%E3%83%88%E3%82%92)
で初めて知った。  

まだ、やってないけど  
{% highlight bash %}
ssh -L 11211:localhost:11211 -p 2022 root@localhost  
{% endhighlight %}
とかすれば、 ssh port fowarding できるので、以下の手順は不要。

追記終わり。

## とりあえず確認するには

とりあえず確認するには  
boot2docker ssh して ifconfig | grep -A4 docker0 すると  
172.... のような IP が見える。  
そこに対して telnet すると ちゃんと疎通できてる。  
ちなみに telnet localhost 11211 しても同様。  
  
あとは mac -> boot2docker に 11211 疎通できればよい。  
ぐぐったら
[http://fogstack.wordpress.com/2014/02/09/docker-on-osx-port-forwarding/](http://fogstack.wordpress.com/2014/02/09/docker-on-osx-port-forwarding/) の記事が見つかったのでほぼこれの通りに、

{% highlight bash %}
boot2docker down
したあと
VBoxManage modifyvm "boot2docker-vm" --natpf1 "guestmemcached,tcp,,11211,,11211"
boot2docker up
docker run -d -p 11211:11211 vimtaku/memcached_1
{% endhighlight %}
これで mac から telnet localhost 11211 で行けた。たぶん。

