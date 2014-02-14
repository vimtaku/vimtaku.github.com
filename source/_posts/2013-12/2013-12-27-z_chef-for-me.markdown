---
layout: post
title: "chef_for_me"
description: ""
category: "chef"
tags: [devops, chef]
---

{% include JB/setup %}

# chef-solo, vagrant に慣れるためにそれっぽいチュートリアルをやる

## tutorial っぽいのがあったのでやってみる
[http://vialstudios.com/guide-authoring-cookbooks.html](http://vialstudios.com/guide-authoring-cookbooks.html)

## chef, chef-solo について
ここが詳しい。
[http://knowledg.sakura.ad.jp/tech/867/](http://knowledge.sakura.ad.jp/tech/867/)

## start

基本的に自分のメモなのであんまり参考にならないと思うけど残しておく。  


 - git install
   - すでにあったので省略
 - Install rbenv and ruby-build
   - すでにあったので省略
 - Install Ruby
   - すでにあったので省略
 - Install Berkshelf
   - すでにあったので省略
 - foodcritic を入れる。

{% highlight bash %}
$ gem install foodcritic
{% endhighlight %}

 - Install VirtualBox
   - すでにあったので省略

 - Install Vagrant
   - すでにあったので省略

 - Creating the Cookbook

{% highlight bash %}
/Users/vimtaku/vm/myface% berks cookbook myface --foodcritic
      create  myface/files/default
      create  myface/templates/default
      create  myface/attributes
      create  myface/definitions
      create  myface/libraries
      create  myface/providers
      create  myface/recipes
      create  myface/resources
      create  myface/recipes/default.rb
      create  myface/metadata.rb
      create  myface/LICENSE
      create  myface/README.md
      create  myface/Berksfile
      create  myface/Thorfile
      create  myface/chefignore
      create  myface/.gitignore
         run  git init from "./myface"
      create  myface/Gemfile
      create  myface/Vagrantfile
{% endhighlight %}

クックブックのスケルトンテンプレートを、 myface っていう名前で作成した。

 - Prepare your virtual environment
   - vendor/bundle にインストールする設定をやってなかったらやっておいたほうが良いかも
     -  [http://qiita.com/toshiwo/items/4e7c82852f3e14bf5a1d](http://qiita.com/toshiwo/items/4e7c82852f3e14bf5a1d)
   - bundle install

 - Starting your virtual machine
   - とりあえず言われたとおりにやってみろってことなのでやってみる

{% highlight bash %}
/Users/vimtaku/vm/myface/myface% bundle exec vagrant up
   ringing machine 'default' up with 'virtualbox' provider...
   [default] Box 'Berkshelf-CentOS-6.3-x86_64-minimal' was not found. Fetching box from specified URL for
   the provider 'virtualbox'. Note that if the URL does not have
   a box for this provider, you should interrupt Vagrant now and add
   the box yourself. Otherwise Vagrant will attempt to download the
   full box prior to discovering this error.
   Downloading or copying the box...
   Progress: 22% (Rate: 1045k/s, Estimated time remaining: 0:03:45)load: 1.56  cmd: curl 27613 waiting 0.84u 1.48s
   Progress: 50% (Rate: 1068k/s, Estimated time remaining: 0:02:03)load: 1.47  cmd: curl 27613 waiting 1.76u 3.18s
   Progress: 51% (Rate: 1147k/s, Estimated time remaining: 0:02:01)^U
   Progress: 85% (Rate: 1061k/s, Estimated time remaining: 0:00:38)load: 1.27  cmd: curl 27613 waiting 2.72u 4.99s
   Extracting box...te: 2251k/s, Estimated time remaining: 0:00:01)j
   Successfully added box 'Berkshelf-CentOS-6.3-x86_64-minimal' with provider 'virtualbox'!
   There are errors in the configuration of this machine. Please fix
   the following errors and try again:
   
   SSH:
   * The following settings shouldn't exist: max_tries, timeout
{% endhighlight %}

   - エラー。 ssh
     - https://github.com/berkshelf/berkshelf/pull/856
     - vagrant バージョンの違いによるものっぽい、削除して try

{% highlight bash %}
/Users/vimtaku/vm/myface/myface% bundle exec vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
[default] Importing base box 'Berkshelf-CentOS-6.3-x86_64-minimal'...
[default] Matching MAC address for NAT networking...
[default] Setting the name of the VM...
[default] Clearing any previously set forwarded ports...
[Berkshelf] This version of the Berkshelf plugin has not been fully tested on this version of Vagrant.
[Berkshelf] You should check for a newer version of vagrant-berkshelf.
[Berkshelf] If you encounter any errors with this version, please report them at https://github.com/RiotGames/vagrant-berkshelf/issues
[Berkshelf] You can also join the discussion in #berkshelf on Freenode.
[Berkshelf] Updating Vagrant's berkshelf: '/Users/vimtaku/.berkshelf/default/vagrant/berkshelf-20131227-27600-dzfcmn-default'
[Berkshelf] Using myface (0.1.0) from metadata
[default] Creating shared folders metadata...
[default] Clearing any previously set network interfaces...
[default] Preparing network interfaces based on configuration...
[default] Forwarding ports...
[default] -- 22 => 2222 (adapter 1)
[default] Booting VM...
[default] Waiting for machine to boot. This may take a few minutes...
[default] Machine booted and ready!
[default] Setting hostname...
[default] Configuring and enabling network interfaces...
[default] Mounting shared folders...
[default] -- /vagrant
[default] -- /tmp/vagrant-chef-1/chef-solo-1/cookbooks
[default] Running provisioner: chef_solo...
Generating chef JSON and uploading...
Running chef-solo...
[2013-12-27T09:05:00+00:00] INFO: *** Chef 10.14.2 ***
[2013-12-27T09:05:01+00:00] INFO: Setting the run_list to ["recipe[myface::default]"] from JSON
[2013-12-27T09:05:01+00:00] INFO: Run List is [recipe[myface::default]]
[2013-12-27T09:05:01+00:00] INFO: Run List expands to [myface::default]
[2013-12-27T09:05:01+00:00] INFO: Starting Chef Run for myface-berkshelf
[2013-12-27T09:05:01+00:00] INFO: Running start handlers
[2013-12-27T09:05:01+00:00] INFO: Start handlers complete.
[2013-12-27T09:05:01+00:00] INFO: Chef Run complete in 0.017946119 seconds
[2013-12-27T09:05:01+00:00] INFO: Running report handlers
[2013-12-27T09:05:01+00:00] INFO: Report handlers complete
{% endhighlight %}

 - 実行されているのは Vagrantfile の中に含まれていた run_list のところのリストだった
 - cent os とか書いているところは自分の好きな感じで設定できるよとのこと
 - このへんは知ってるけど不安定とかになったら vagrant destroy とかで消せるよとのこと
 
 - Deploying with Artifact Deploy
   - myface っていうスーパークールな sns アプリ作ってやろうぜとのこと
     - Artifact Deploy は LWRP を deploy するためのもの!
       - LWRP はこちらを参照 -> [http://sssslide.com/speakerdeck.com/kentaro/chef-lwrp](http://sssslide.com/speakerdeck.com/kentaro/chef-lwrp)
   - vim で編集. 
{% highlight bash %}
/Users/vimtaku/vm/myface/myface% vim recipes/default.rb

artifact_deploy "myface" do
  version "1.0.0"
  artifact_location "http://dl.dropbox.com/u/31081437/myface-1.0.0.tar.gz"
  deploy_to "/srv/myface"
  owner "myface"
  group "myface"
  action :deploy
end
{% endhighlight %}

  - provision command でもう一回 chef の provision を実行する。
  - berkshelf vagrant plugin の 魔法によって依存関係を勝手に解決してくれる!
  

{% highlight bash %}
[Berkshelf] This version of the Berkshelf plugin has not been fully tested on this version of Vagrant.
[Berkshelf] You should check for a newer version of vagrant-berkshelf.
[Berkshelf] If you encounter any errors with this version, please report them at https://github.com/RiotGames/vagrant-berkshelf/issues
[Berkshelf] You can also join the discussion in #berkshelf on Freenode.
[Berkshelf] Updating Vagrant's berkshelf: '/Users/vimtaku/.berkshelf/default/vagrant/berkshelf-20131227-27600-dzfcmn-default'
[Berkshelf] Using myface (0.1.0)
[default] Running provisioner: chef_solo...
Generating chef JSON and uploading...
Running chef-solo...
[2013-12-27T09:36:54+00:00] INFO: *** Chef 10.14.2 ***
[2013-12-27T09:36:54+00:00] INFO: Setting the run_list to ["recipe[myface::default]"] from JSON
[2013-12-27T09:36:54+00:00] INFO: Run List is [recipe[myface::default]]
[2013-12-27T09:36:54+00:00] INFO: Run List expands to [myface::default]
[2013-12-27T09:36:54+00:00] INFO: Starting Chef Run for myface-berkshelf
[2013-12-27T09:36:54+00:00] INFO: Running start handlers
[2013-12-27T09:36:54+00:00] INFO: Start handlers complete.

================================================================================
Recipe Compile Error in /tmp/vagrant-chef-1/chef-solo-1/cookbooks/myface/recipes/default.rb
================================================================================

NameError
---------
Cannot find a resource for artifact_deploy on centos version 6.3

Cookbook Trace:
---------------
  /tmp/vagrant-chef-1/chef-solo-1/cookbooks/myface/recipes/default.rb:9:in `from_file'

Relevant File Content:
----------------------
/tmp/vagrant-chef-1/chef-solo-1/cookbooks/myface/recipes/default.rb:

  1:  #
  2:  # Cookbook Name:: myface
  3:  # Recipe:: default
  4:  #
  5:  # Copyright (C) 2013 YOUR_NAME
  6:  # 
  7:  # All rights reserved - Do Not Redistribute
  8:  
  9:  artifact_deploy "myface" do

[2013-12-27T09:36:55+00:00] ERROR: Running exception handlers
[2013-12-27T09:36:55+00:00] ERROR: Exception handlers complete
[2013-12-27T09:36:55+00:00] FATAL: Stacktrace dumped to /var/chef/cache/chef-stacktrace.out
[2013-12-27T09:36:55+00:00] FATAL: NameError: Cannot find a resource for artifact_deploy on centos version 6.3
{% endhighlight %}

 - 期待通りにエラッた
    - 俺らは全然 artifact_deploy はいらない
    - chef client が必要としているだけ
    - 俺らは LWRP が必要だっていうのを 俺らの cookbook に教えていないから教えてやる
    
 - Working with cookbook metadata
   - metadata.rb に教えてやる必要がある。
     - このファイルは結構初心者が見落としがちなんだけど結構大事なんだぜ
     - metadata.rb は Rubygems で言う gemspec みたいなやつで依存関係を記述する奴で
       - name attribute で名前とか
       - version attribute でバージョンとか
       - depends 定義で 依存する cookbooks の記述とか
       - conflict 定義で conflict する奴とか
       - maintainer attribute で メンテナとか
       - maintainer email とか
       - license 情報 とか
       - long_description を使って description とか
       をかける!
   - 全部の属性は必要ない
   - けど attribute を空にするのはやめておけとのこと
   
  - 追記してbundle exec vagrant provision を実行

 - ユーザ追記とかして
 
 - Using Berkshelf to gather dependencies
  - どっから dependencies を解決しているのか
   - metadata.rb をみていろいろやる
  - capistrano っぽいディレクトリ構成で deploy
  
 - Refactoring into attributes
   - attributes/default.rb に記入
{% highlight bash %}
default[:myface][:user] = "myface"
default[:myface][:group] = "myface"
{% endhighlight %}


{% highlight bash %}
group "#{node['myface']['group']}"

user "#{node['myface']['group']}" do
  group "#{node['myface']['group']}"
  system true
  shell "/bin/bash"
end

artifact_deploy "#{node['myface']['group']}" do
  version "1.0.0"
  artifact_location "/tmp/myface-1.0.0.tar.gz"
  deploy_to "/srv/myface"
  owner "#{node['myface']['user']}"
  group "#{node['myface']['group']}"
  action :deploy
end
{% endhighlight %}

  - artifact_location に /tmp 指定してるのは回線事情があって wget したやつをおいておいた.
  - うまく行った。書き方をちゃんとしないと validation で引っかかるので注意。
  
 - Idempotent recipes
   - レシピの冪とう性について書かれている
   - not_if とかないと
   
 - Configuring the application server
   - depends "tomcat", "~> 0.11.0" を metadata.rb に追加する
   - include_recipe "tomcat" を recipes/default.rb に追加する
   - provision

 - include_recipe versus a Role with a run_list
   - スパイダーマンより、 大いなる力には、大いなる責任が伴う
   - ポートフォワーディングの設定
     - config.vm.network :forwarded_port, guest: 8080, host: 9090

 - Configuring Tomcat users
   - したがってやったらできた。
   - /myapp はなぜか動かない
   - まぁでもとりあえず受けれるところまで行ければおkなのでここまでとする

 - その下は
   - もっと attribute で可変にするところとか
   - database の LWRP を使う例とかi
 が書いてある

 - これが便利

{% highlight bash %}
require 'json'

file "/tmp/dna.json" do
  content JSON.pretty_generate(node)
end
{% endhighlight %}


 
 



