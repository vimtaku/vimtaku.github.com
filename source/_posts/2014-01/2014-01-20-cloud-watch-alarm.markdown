---
layout: post
title: "chef で opsworks のインスタンス起動時に cloudwatch の alarm を作成するためにしたこと"
description: "chef で opsworks のインスタンス起動時に cloudwatch の alarm を作成するためにしたこと"
category: "aws"
tags: [aws, cloudwatch, opsworks, chef]
keywords: "aws cloudwatch opsworks chef"
---

{% include JB/setup %}

## TOC
* 
{:toc}

## 大まかな流れ
1. IAM の設定
2. SNS のトピックの作成(メール送る用)
3. cloud watch の API を cli から叩いてみる
4. cloud watch の手動確認
5. chef recipe を書く
6. opsworks のレイヤー設定で、 setup ライフサイクルイベントで設定

ではさっそく。  

## 1. IAM の設定
opsworks ではインスタンス生成時に デフォルトで Default IAM instance profile を設定できる。  
ここの IAM Role に権限がないと、 aws cli から操作ができないので注意する。
この記事では、IAMの設定については詳細に明記しない。  

## 2. SNS のトピックの作成
コマンドラインか AWS Console から、メール送る用に トピックを作成する。  
監視しているインスタンスの異常時にメールを受け取るため。  
CLI ではこちらが参考になる。  
[http://exploreaws.doorblog.jp/archives/24701093.html](http://exploreaws.doorblog.jp/archives/24701093.html)  
サブスクリプションを許可しておくと後々のチェックで捗る。  
ちなみにこれは、あとで alarm-actions に指定する。  

ここではできた topic が、仮に arn:aws:sns:ap-northeast-1:99999999:mogemoga としておく。  

## 3. cloud watch の API を cli から叩いてみる

### メトリックの作成 from cli

{% highlight bash %}
aws cloudwatch put-metric-alarm
--alarm-name='disk-usage' \
             --alarm-description 'Disk usage alert' \
             --alarm-actions='arn:aws:sns:ap-northeast-1:99999999:mogemoga'   \
             --namespace  'System/Linux'  \
             --metric-name  'DiskSpaceUtilization'   \
             --dimensions='[{"Name": "InstanceId","Value": "i-deadbeaf"},{"Name":"Filesystem","Value": "/dev/xvda1"},{"Name":"MountPath","Value":"/"}]' \
             --statistic  'Average' \
             --period  '300'   \
             --unit 'Percent'
             --threshold  '50'
             --evaluation-periods  '1'
             --region 'ap-northeast-1'
             --comparison-operator 'GreaterThanOrEqualToThreshold'
{% endhighlight %}

ここですんなりうまくいくかどうかが別れると思う。  
IAM role を使う場合は、権限が許可されていれば問題なく通ると思う。  
権限がない場合は、 cli の credential 設定が必要になるかもしれない。  
[http://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/SettingUp_CommandLine.html](http://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/SettingUp_CommandLine.html)  

## 4. cloud watch での作成確認
上記のコマンドで成功していれば AWS Console から cloud watch のアラームができているはずなので確認する。  

## 5. chef recipe を書く

あとは、上記 3 を実行する chef のレシピを書いて、 opsworks が取ってくる custom chef recipe のレポジトリに配置すればおｋ．  
今回は load average もとりたかったので、  
[http://aws.amazon.com/code/8720044071969977](http://aws.amazon.com/code/8720044071969977)  
のスクリプトを改変して使用することにした。  
ソースはこちら -> [https://github.com/FumihikoSHIROYAMA/cloud_watch_script.git](https://github.com/FumihikoSHIROYAMA/cloud_watch_script.git)  
disk space をとったりすることができる pl なのですごく便利。  

下記の repository は crontab に 上記の pl (load average は別) のものを登録するものだ。  
[https://github.com/alexism/cloudwatch_monitoring](https://github.com/alexism/cloudwatch_monitoring)  

これをフォークして、 load average とれる cron を作る機能と、 アラーム登録する奴を書いた。  
[https://github.com/vimtaku/cloudwatch_monitoring.git](https://github.com/vimtaku/cloudwatch_monitoring.git)  
chef 力低めで申し訳なので、アドバイス、pull req など大歓迎。  

## 6. opsworks のレイヤー設定で、 setup ライフサイクルイベントで設定
これはもう、 opsworks に慣れているひとなら問題無いと思う。  
5 で作ったものがうまくいっていれば(というか試すときに deploy かどっかで試すと思うんだけど)  
それを setup に移動してやるだけだ。  


### おまけ

#### 気をつけること要点
 - IAM Role に権限がない場合があるので気をつける
   - 参考
     - [http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/UsingIAM.html](http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/UsingIAM.html)
     - [http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-CreateVolume.html](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-CreateVolume.html)

 - opsworks では chef の data_bags が使えないのを頭に入れておく
   - 自分はあとから気づいたパターン
     - 詳細は [http://docs.aws.amazon.com/opsworks/latest/userguide/workingcookbook-chef11.html](http://docs.aws.amazon.com/opsworks/latest/userguide/workingcookbook-chef11.html)
 - mon-put-metric-alarm の代わりに aws cloudwatch put-metric-aaerm を使用する
   - role の設定ができていても credential エラーとかいっぱい言われるため。
 - alert と alarm が似ているので alerm ってタイポに気をつける

#### 標準出力から入力をし続けるからメモリに確保し続けるコマンド
{% highlight bash %}
/dev/null < $(yes)
cat - < $(yes)
{% endhighlight %}
