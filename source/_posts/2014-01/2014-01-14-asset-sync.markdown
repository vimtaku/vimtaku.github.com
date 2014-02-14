---
layout: post
title: "asset_sync"
description: ""
category: "chef"
tags: [aws, opsworks, chef]
---

{% include JB/setup %}

## rails で asset pipeline を使って s3 に上げる手順について(opsworks使用)

## 参考URL
こちらを参考にした。
[http://d.hatena.ne.jp/lettas0726/20130320/1363773153](http://d.hatena.ne.jp/lettas0726/20130320/1363773153)

#### Gemfile
{% highlight ruby %}
gem 'uglifier', '>= 1.3.0'
gem 'asset_sync'
{% endhighlight %}
を追記。

#### deploy/before_migrate.rb

{% highlight ruby %}
template "#{release_path}/config/asset_sync.yml" do
  mode "0644"
  source "asset_sync.yml.erb"
end

bash "precompile_assets" do
  user "deploy"
  cwd release_path
  code 'bundle exec rake assets:precompile RAILS_ENV=production'
end
{% endhighlight %}

今回は、 before_migrate で呼ぶことにする。  
chef-solo で呼ばれるので resource が使える模様。  
カスタム chef リポジトリに asset_sync.yml の template を用意しておく。  
template は asset_sync.yml.erb としておいておく感じ。

#### config/environments/production.rb
hoge.example.com のバケットとしたとき、以下のように追記。

{% highlight ruby %}
config.action_controller.asset_host = '//s3-ap-northeast-1.amazonaws.com/hoge.example.com'
config.assets.initialize_on_precompile = true
{% endhighlight %}

## existing_remote_files オプションについて
アセットパイプラインでは、ファイルにおそらく md5 の hash 値がついているのを生成する。  
ここで複数の rails インスタンスが非同期(もしくは片方のみ)なので  
existing_remote_files: 'delete' として  
precompile を実行するともともと参照していたファイルが消えてしまうので、  
config/asset_sync.yml の中の existing_remote_files を keep にする。  
(しかし、ゴミはのこる。対処については要検討。  

