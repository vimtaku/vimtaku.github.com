---
layout: post
title: "vim-operator-mdurl という vim plugin 書いた"
description: "vim-operator-mdurl という vim plugin 書いた"
category: "vim"
tags: [vim]
---
## はじめに
この記事は [Vim Advent Calender 2013](http://atnd.org/events/45072) の 98 日目 の記事になります。

## 背景
Vimmer のみなさんが blog を書くなら普通に vim で markdown を書いていることと思います。  
例に違わず僕も markdown で blog を書いているんですが、非常に不満に思っていたことがありました。  
それは url を書くときそれを[]() で囲わなきゃならなかったことです。  
まぁ書き慣れりゃ楽なんですけど最初は()[] なのか []() なのかわからなくて逆になって辛いみたいな  
ことが起こっていました。  
http://example.com みたいな url があった時に,  
簡単に [http://example.com](http://example.com) とできるような plugin が欲しかったのです。  
正直言うと幾つか方法があって, 保存時に変換とかもやりようによっちゃあるんですが  
僕はおせっかいが嫌なので自分で変換できる operator が欲しかったのです。  

## 概要
http://example.com という文字列を  
{% highlight vim %}  
[http://example.com](http://example.com)
{% endhighlight %}
と簡単に変換できる operator plugin を書きました。  
また, blog を書くときに参考資料とかに url を memo で書いていて,  
それを
{% highlight vim %}  
[ここのリンク](http://example.com)
{% endhighlight %}
みたいなふうに  
作りたいというのがよくあったので, yank している url を 文字列にリンク展開できるようにもしました。  

## 成果物
[https://github.com/vimtaku/vim-operator-mdurl](https://github.com/vimtaku/vim-operator-mdurl)  

### 使用方法

#### 0. (option) vim-textobj-url をインストール  
なくてもいいんだけど、あったら多分便利だと思います。
{% highlight vim %}  
NeoBundle 'mattn/vim-textobj-url'  
{% endhighlight %}  

#### 1. vim-operator-mdurl を インストール  
.vimrc に  
{% highlight vim %}  
NeoBundle 'vimtaku/vim-operator-mdurl'
{% endhighlight %}  
#### 2. .vimrc に map を書く  
設定例)  
{% highlight vim %}
map L <Plug>(operator-mdurl)
map M <Plug>(operator-mdurlp)
{% endhighlight %}
#### 3. 使う  
http://example.com とかの文字列の何処かで LiW とかするとできます。  
http://example.com を yank しておいて hoge とかの文字列上で Miw とかすると、  
[hoge](http://example.com) になります。  

0 の手順で url のテキストオブジェクトプラグインを入れておいたら LiW とかの W の部分が  
デフォルトでは u で統一できて楽。  

### 直接関係ないけど、text-obj-user の .vimrc の設定について

vim-textobj-user では map を変えられます。  
textobj-wiw ですでに u は使っていたので  
text-obj-url のデフォルトの u(i|a) とかぶると嫌なので以下の様に書きました。  

{% highlight vim %}
xmap ah  <Plug>(textobj-url-a)
omap ah  <Plug>(textobj-url-a)
xmap ih  <Plug>(textobj-url-i)
omap ih  <Plug>(textobj-url-i)

xmap au  <Plug>(textobj-wiw-a)
omap au  <Plug>(textobj-wiw-a)
xmap iu  <Plug>(textobj-wiw-i)
omap iu  <Plug>(textobj-wiw-i)
{% endhighlight %}

## 所感
最近は vim に注力出来ていなかったけど、ずっと書きたかった plugin がかけて良かったです。  
もし良かったら使ってみてください。  
[VAC 2013](http://atnd.org/events/45072) にも参加できてとりあえず良かったです。  
バグはちょろちょろ直していきます。  
