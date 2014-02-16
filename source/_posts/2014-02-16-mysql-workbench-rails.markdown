---
layout: post
title: "MySQL Workbench で Rails の migration ファイルを吐き出す"
description: "MySQL Workbench で Rails の migration ファイルを吐き出す"
category: "rails"
tags: ["rails", "mysql", "MySQLWorkbench"]
---

## TOC
* 
{:toc}

## 概要
mysql workbench っていう MySQL のER図を作るみたいなツールがある。  
CUI硬派のため、いままでは使ったことなかったけど、  
今回はじめて使ってみると結構いい感じだった。  
それを rails のマイグレーションファイルに吐き出したいなぁと思っていたら、  
それっぽいプラグインを見つけたので、その方法を書いておく。  

## Mysql workbench の SS
<img src="http://gyazo.com/f15f8824bbfdd3bb3dccd514b697f5e5.png" />

## 手順

### 1. mysql workbench rails exporter plugin をダウンロード
[http://sourceforge.net/projects/railsexporter/](http://sourceforge.net/projects/railsexporter/)
ここから、 rails exporter を落としてくる。  

### 2. mysql workbench のインストール
新しい workbench ツールに、上記プラグインが対応していなかったので、 ちょっと古いやつで試したら動作した。  
[http://dev.mysql.com/downloads/tools/workbench/](http://dev.mysql.com/downloads/tools/workbench/) から、
Looking for previous GA versions? をたどると、 mysql-workbench-gpl-5.2.47-osx-i686.dmg  が落とせる。  
これをインストールする。  

### 3. プラグインのインストールと実行
mysql workbench のタブから、 "SCRIPTING > INSTALL PLUGIN/MODULE ..." で、展開した 1 の zip の .lua ファイルを選択する。  
mysql workbench を再起動して、 "PLUGINS > CATALOG > Rails Exporter 1.0.1" を選択すれば、あら不思議、  
$HOME に Rails_Explorer ディレクトリが。  
db と model が出来上がるので、あとはそれをよしなに利用すれば良い。  


## 所感
実際、このあとに、 mysql に接続して、ER図を吐き出すやつやってみたけど、  
まぁ当然、テーブル的にリレーションしてないわけだから、ER図もリレーションしてなくて、見づらい。  
raisl には ERD 生成ツールみたいなのがあるらしいので、  
最初の一回くらいしか使えないかもしれないけど、ないよりはいいかなぁ。  
ちなみに出来上がるファイルは若干修正が必要だと思う。  
ちゃんと mwb のほうで書いていれば大丈夫かもだけど。。  
mwb の方で、カラムに複数行のコメントを入れておくと、出力された時にダメなので、  
コメントを1行にするか、複数行の場合は先頭に#を入れると良いかもしれない。  
あと、 unsigned int を作りたいけど、うまいことやってくれなくて、  
gem "activerecord-mysql-unsigned", "~> 0.0.1"
これを入れて、手動で integer のカラムに :unsgined => true した。  

