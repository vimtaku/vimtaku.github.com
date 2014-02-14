---
layout: post
title: "opsworks and dotenv"
description: ""
category: "aws"
tags: [opsworks, ruby]
---
{% include JB/setup %}

## もくじ
* 
{:toc}

<br />

## opsworks の cookbook 処理順について
opsworks では built-in cookbook -> custom cookbook の順に処理されて、  
custom cookbook でいい感じにディレクトリを配置しても上書きはできない。  
(ただし、 built-in cookbook で使用される recipe の attribute に適切な値を設定することは可能。)  
[http://docs.aws.amazon.com/opsworks/latest/userguide/customizing.html](http://docs.aws.amazon.com/opsworks/latest/userguide/customizing.html)

## opsworks と dotenv の依存関係
opsworks の built-in レシピの deploy::rails で使用する環境変数は、当然 .env に依存するのだけど、  
.env の生成は custom cookbook でやっていて、上記にも書いたとおり上書きもできないから  
rails は .env.test とか読んじゃって辛い。  
これ回避する方法あるのかなぁ。。  

## (2014/1/26追記) 解決方法
load balancer の死活チェックを health_check.html とかを見るようにしておいて、  
built in recipe の deploy 時点で一回 unicorn 立っちゃうけど
user recipe の deploy のところで dotenv を作って、  
unicorn とか再起動したのち、 health_check とかを  
おけば割と問題ないんじゃないかってことになってる。  

## (2013/12/26追記) 解決方法2つ
考えた方法を2つ。  

## 参考情報とか
このへんとか参考になりそうなんだけど、いまいち確信 answer はない。  
[https://forums.aws.amazon.com/forum.jspa?forumID=153&start=0](https://forums.aws.amazon.com/forum.jspa?forumID=153&start=0)

### シンボリックリンクを使う
apps のリポジトリに予め、 .env -> /etc/.env みたいなシンボリックリンクをコミットしておいて、  
deploy::rails の前に dotenv を配置する recipe で配布する。  

### {appname}/deploy/before_migrate.rb を使う
[http://docs.opscode.com/resource_deploy.html](http://docs.opscode.com/resource_deploy.html)  
こいつがデプロイ時(というかマージ実行時)に呼ばれるためのフックでいい感じに recipe を呼ぶ。  
これを参考にした[https://github.com/drnic/mydemoapp](https://github.com/drnic/mydemoapp)。  
とりあえず消えたらあれなのでフォークしておいた.  
[https://github.com/vimtaku/mydemoapp](https://github.com/vimtaku/mydemoapp)  

