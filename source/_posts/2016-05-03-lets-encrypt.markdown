---
layout: post
title: "let's encrypt で ec2 で単独で動いているサービスを https 化する"
date: 2016-05-03 16:19:17 +0900
comments: true
categories: ["lets_encrypt"]
tags: ["cert"]
---

## はじめに
前提として、 EC2 に passanger + nginx でサービスを動かしている。  
Let's encrypt を導入するには主に以下をやる必要があるだろう。

1. サーバにログインして letsencrypt-auto スクリプトを使用して証明書を作成
2. nginx に設定し、再起動
3. 90日で切れるので、証明書の自動更新をつける

以下では、  
 - force SSL 的な設定は今のところ nginx などではやらない。  
 - クライアント側のアプリなどで対応する。  

## 1. 証明書を作成
```
cd /opt
git clone https://github.com/letsencrypt/letsencrypt
cd /opt/letsencrypt
# ec2 のセキュリティグループで 443 を開けておく
./letsencrypt-auto certonly --standalone
```

## 2. nginx に設定し、再起動

以下をいい感じに追加する。

```
       listen 443 ssl;

       ssl_certificate /etc/letsencrypt/live/your.domain.name/fullchain.pem;
       ssl_certificate_key /etc/letsencrypt/live/your.domain.name/privkey.pem;
       ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
       ssl_prefer_server_ciphers on;
       ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';
```

## 3. 自動更新

su で crontab -e

```
00 05 01 * * /etc/init.d/nginx stop; /opt/letsencrypt/letsencrypt-auto certonly --standalone -d your.domain.name --renew-by-default --debug ; /etc/init.d/nginx start;
```

## 所感
https 化が簡単にできて最高な時代だなぁと思った。

## 参考
[https://www.mitchcanter.com/lets-encrypt-ssl-amazon-aws/](https://www.mitchcanter.com/lets-encrypt-ssl-amazon-aws/)
