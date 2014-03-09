---
layout: post
title: "Amazon Linux に phpmyadmin を入れてクライアント認証する"
description: "Amazon Linux に phpmyadmin を入れてクライアント認証する"
category: "dev"
tags: ["phpmysqladmin", "client_auth"]
---

## TOC
* 
{:toc}


## 概要
amazon linux で phpmyadmin(httpd) を入れてクライアント認証するパターンになったのでやってみた。  

## 下準備

{% highlight bash %}
## apache と phpmyadmin が入る
sudo yum --enablerepo=epel install phpmyadmin
## mod_ssl
sudo yum install mod24_ssl
sudo yum install openssl-devel
{% endhighlight %}

## DB の設定をあれこれする
{% highlight bash %}
sudo vim /etc/phpMyAdmin/config.inc.php
{% endhighlight %}

## phpmyadmin のアクセス制限,今回は クライアント認証する前提なので全開放
{% highlight bash %}
sudo vim /etc/httpd/conf.d/phpMyAdmin.conf
{% endhighlight %}

とりあえず全開放
{% highlight bash %}
## L16 あたりに
Require all granted
## L26 あたりに
Allow from 0.0.0.0
{% endhighlight %}


{% highlight bash %}
/etc/httpd/conf.d/virtualhost.conf
{% endhighlight %}
{% highlight apache %}
<Directory "/phpmyadmin">
    Options ExecCGI
    AllowOverride all
    Order Allow,Deny
    Allow from all
    RewriteEngine On
    RewriteCond %{SERVER_PORT} 80
    RewriteRule ^(.*)$ https://%{HTTP_HOST}/%{REQUEST_URI} [R,L]
    LogLevel alert rewrite:trace3
</Directory>
{% endhighlight %}



## クライアント認証
については
[この資料](https://github.com/mechamogera/MyTips/wiki/Apache%E3%81%A7%E3%82%AF%E3%83%A9%E3%82%A4%E3%82%A2%E3%83%B3%E3%83%88%E8%AA%8D%E8%A8%BC%E3%81%97%E3%81%A6%E3%81%BF%E3%82%8B)
がものっすごい参考になる。  
というかこのままやれば良い。  

### p12 のインストールについて
Google Chrome  はキーチェーンに入れたら行けた。  
Vimperator(Firefox) についてはブラウザに pom.p12 を入れたら行けた。  


## 参考文献
phpmyadmin についてはこれが非常に役に立つ。  
[http://sanketdangi.com/post/56623052533/phpmyadmin-on-amazon-ec2-manage-amazon-rds](http://sanketdangi.com/post/56623052533/phpmyadmin-on-amazon-ec2-manage-amazon-rds)  
クライアント認証に関しては  
[https://github.com/mechamogera/MyTips/wiki/Apache%E3%81%A7%E3%82%AF%E3%83%A9%E3%82%A4%E3%82%A2%E3%83%B3%E3%83%88%E8%AA%8D%E8%A8%BC%E3%81%97%E3%81%A6%E3%81%BF%E3%82%8B](https://github.com/mechamogera/MyTips/wiki/Apache%E3%81%A7%E3%82%AF%E3%83%A9%E3%82%A4%E3%82%A2%E3%83%B3%E3%83%88%E8%AA%8D%E8%A8%BC%E3%81%97%E3%81%A6%E3%81%BF%E3%82%8B)  

