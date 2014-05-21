---
layout: post
title: "クライアント認証をもう一度やる@nginx"
description: "クライアント認証をもう一度やる@nginx"
category: "dev"
tags: ["apache", "nginx", "client_auth"]
---

## TOC
* 
{:toc}

## 概要
前回は、資料のようにやればできるって感じだったけど、  
理解して一つづつもう一度やってみたかったのでもう一度やってみた。  

## やったこと
基本的には以下のサイトにしたがってすすめた。  
[こちらのサイト](http://server-setting.info/centos/private-ca-cert.html)  
基本的に前回の記事では、CA(.sh) という、  
openssl の ca を扱うのに便利な付属スクリプトは使っていなかったんだけど、  
今回のは CA(.sh) を使ってすすめた。  
というか、ほとんどやったことは上記のサイト通りなので、上記のサイト通りやると良いとおもう。  
解説もものすごく丁寧にされているので。  
なので、自分がやったことの補足だけ書いていく。  

### 補足

#### config による直接的な制限
まず、背景として、vimtaku, hogetaku というユーザにそれぞれクライアント認証したい。  
それで、それぞれのクライアントの鍵はサーバで作成する。  

なので、サーバ側で  

  - クライアント用(vimtaku,hogetaku二人別々)の公開鍵と秘密鍵を作り、  
  - csr をだし、
  - それを ca が 署名して、
  - pfx に変換して

最終的に、配りたい対象ユーザのブラウザに渡すかんじになった。

client認証はしていて、さらに config レベルで、アクセスを制限したい場合には、
ssl.conf に
{% highlight apache %}
<Location />
SSLRequire (    %{SSL_CIPHER} !~ m/^(EXP|NULL)/ \
            and ( \
                  %{SSL_CLIENT_S_DN_CN} eq "vimtaku" \
                  or %{SSL_CLIENT_S_DN_CN} eq "hogetaku" \
                ) \
            )
</Location>
{% endhighlight %}
などと書けば設定できる。  
  
他にも条件は書けそうなので、パラメータに関しては[こちらを参考](http://httpd.apache.org/docs/2.4/mod/mod_ssl.html)に。  

デバッグとしては
{% highlight apache %}
CustomLog logs/ssl_request_log \
  "%t %h %{SSL_PROTOCOL}x %{SSL_CIPHER}x %{SSL_CLIENT_S_DN_O}x %{SSL_CLIENT_S_DN_CN}x %{SSL_CLIENT_S_DN}x \"%r\" %b"
{% endhighlight %}
としてログだすとよいかもしれない。

#### revoke による証明書の失効
config ではなく、 revoke で、たとえば hogetaku が会社辞めたとかで配った証明書を失効させる方法として、  

{% highlight bash %}
cd /etc/pki/CA
cp serial crlnumber
openssl ca -gencrl -revoke  /etc/pki/CA/certs/hogetaku.ec2-xxxxxxxxxxxxx.ap-northeast-1.compute.amazonaws.com.crt -config /etc/pki/tls/openssl-client.cnf
cd /etc/pki/CA/crl/
openssl ca -gencrl -out crl.pem
{% endhighlight %}
として、

そして  
/etc/httpd/conf.d/ssl.conf に、
{% highlight apache %}
SLCACertificateFile /etc/pki/CA/cacert.pem
{% endhighlight %}
の下くらいに  
{% highlight apache %}
SSLCARevocationCheck chain
SSLCARevocationFile /etc/pki/CA/crl/crl.pem
{% endhighlight %}
を追加した。  

それで、サーバを再起動すれば、revoke した証明書を持つクライアントはアクセスできなくなる(400)。

ちなみに revoke は
/etc/pki/CA/newcerts/内のファイル か /etc/pki/CA/newcerts/certs 内のファイルのどちらでもよさそう。  

## nginx による、SSL 設定とクライアント認証
はっきり言ってなんの問題もない

{% highlight bash %}
sudo yum -y install nginx
{% endhighlight %}

/etc/nginx/conf を編集。  

{% highlight nginx %}
 server {
        listen       443;
        server_name  localhost;

        ssl                  on;
        ssl_certificate      /etc/pki/CA/certs/ec2-xxxxxxxxxxxxx.ap-northeast-1.compute.amazonaws.com.crt.pem;
        ssl_certificate_key  /etc/pki/CA/private/ec2-xxxxxxxxxxxxx.ap-northeast-1.compute.amazonaws.com.key;
        ssl_verify_client on;
        ssl_client_certificate /etc/pki/CA/cacert.pem;
        ssl_crl /etc/pki/CA/crl/crl.pem;

        ssl_session_timeout  5m;

        ssl_protocols  SSLv2 SSLv3 TLSv1; 
        ssl_ciphers  HIGH:!aNULL:!MD5; 
        ssl_prefer_server_ciphers   on;

        location / {
            root   html;
            index  index.html index.htm;
        }
    }
{% endhighlight %}

大体、 apache と同じような設定を行えば使用できる。  

### ちなみに
revoke したのをやっぱ辞めたいって場合は  
/etc/pki/CA/index.txt の  
R の行の R を V にして、 その次の次のカラムの 時間を 消してから、  
openssl ca -gencrl -out crl.pem  
してやればよい。  

## 所感
複雑だけど、さすがに2週連続で日曜日使えばだいたいわかる。  
でももっと奥深く知るにはちゃんと本を読むべきだと思う。  

