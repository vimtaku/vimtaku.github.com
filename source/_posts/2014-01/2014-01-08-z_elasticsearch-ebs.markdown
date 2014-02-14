---
layout: post
title: "elasticsearch_ebs"
description: ""
category: "aws"
tags: [aws, devops, elasticsearch]
---

{% include JB/setup %}

# opsworks と elasticsearch についてのメモ

## elasticsearch についての memo
 - クラスタリングした時に master-slave みたいになるのか？
   - ならない。すべてがマスタになる模様
   - 複数台立てておいて、片方落としておいて、立っている方にデータPUT して落としておいた方を立ててGETできる
 - elastic search s3 gateway はパフォーマンスの問題で deprecated になっている模様
   - なので EBS でやることになる。
 - chef の recipe を見ると、設定しておけば ebs を自動で作ってマウントまでしてくれる感じになっている
   - elasticsearch::ebs, elasticsearch::data のレシピを実行すれば良い感じ。
     - opsworks では, layers に elasticsearch::ebs, elasticsearch::data の順で登録すればおｋ
     - README に書いてある通りに設定が必要。具体的には data_bags/elasticsearch/data.json と aws の認証周りの設定が必要
 - 各アベイラビリティゾーンに分散しておいておいて、 internal loadbalancer に紐付ければ、片方が死んでも大丈夫
 - 災害時の復旧については、たぶん snapshot からの復旧に加えて、 index の再作成(upsert なAPI 叩きまくる) が必要
 - discovery については aws-instance プラグインみたいなのがあって、api 叩いてリスト取得してきている。
 
## elasticsearch についての memo2
 - elasticsearch って aws のサービス名かと思ってたけど、別にそういうわけじゃない
 
 
## elasticsearch についての memo3
opsworks のレイヤーで、internal LB をひもづけることはできると思うけど、  
新しく作った elasticsearch node のインデックス作成とかしている時に紐づくとまずいので  
いったん手動でやるようにする。(たぶんELBの死活監視を工夫すればうまいこと行ける)  


## opsworks の chef で役立つコマンド例
    
{% highlight bash %}

## setup サイクルを retry
opsworks-cli-agent run_command setup

## custom.json を dump
opsworks-cli-agent get_json > hoge.json

## ログをみる
opsworks-cli-agent show_log

## hoge.json を指定すれば、楽にもう一度ライフサイクルを指定できる
"/opt/aws/opsworks/current/bin/chef-solo" -j "hoge.json" -c "/opt/aws/opsworks/current/conf/solo.rb" -L "/var/lib/aws/opsworks/chef/2014-01-09-05-28-54-01.log" > "/var/lib/aws/opsworks/chef/2014-01-09-05-28-54-01.log.out" 2>&1


### 役に立つディレクトリ

## opsworks の chef-solo が走るときにログが出たり、走るときの json が出たりする
/var/lib/aws/opsworks

## opsworks の chef-solo のクックブックとかがこのへんにある。
/opt/aws/opsworks

{% endhighlight %}

## use_iam_profile がうまく行かない件
gem の fog を使うところで use_iam_profile => true で渡しても全然うまく動かない問題があって、  
ライブラリのバグかもと思ったけどなんか違うっぽくて、時間がなかったので custom.json で大人しく accesskey とかを指定した。  
おそらく _request の時に credential の作成をしているんだけど、そこでミスっているんじゃないかなぁという予想。

## 参考資料というか調べてく時に必要だったこと。
 - [https://github.com/elasticsearch/elasticsearch/issues/2458](https://github.com/elasticsearch/elasticsearch/issues/2458)
 - [http://www.elasticsearch.org/tutorials/elasticsearch-on-ec2/](http://www.elasticsearch.org/tutorials/elasticsearch-on-ec2/)
 - [http://www.smokeymonkey.net/2013/10/elasticsearchcluster.html](http://www.smokeymonkey.net/2013/10/elasticsearchcluster.html)
 - [http://docs.aws.amazon.com/opsworks/latest/userguide/troubleshoot-debug-cli.html](http://docs.aws.amazon.com/opsworks/latest/userguide/troubleshoot-debug-cli.html)
 
