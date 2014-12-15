---
layout: post
title: "clojure で 一部分だけテストがしたい using midje"
description: "clojure で 一部分だけテストがしたい using midje"
category: "midje"
tags: [midje, clojure]
keywords: "midje, clojure"
---

{% include JB/setup %}

## TOC
* 
{:toc}

## ruby と比べて、 clojure のテスト
普段書いている ruby と比べて、圧倒的に clojure の方が良いところはテストの実行速度だ。  
比べ物にならないくらい早い。DB にデータとか入れてないからかもしれないけど。  
基本的に保存したら結果が出ている。 repl の立ち上がりとか、最初のテスト読み込みとかはクソ重いけども。  
さて、普段は rspec でテストを書いているが、 describe "hoge test ", filter:true do ... とかすると  
filter ができるのだが、 clojure ではどうやるのだろうか。調査してみた。  

## test のメソッド filter をどうやるか？

{% highlight clojure %}
; in repl.clj
(defn filter-autotest []
(require 'midje.repl) (midje.repl/autotest :filter (fn [fact] (:filter fact) ))
)

(defn all-autotest []
(require 'midje.repl) (midje.repl/autotest :filter (fn [fact] true))
)

; in test
(fact :filter "do something great test"
 (is true)
)
{% endhighlight %}

repl.clj みたいなものを作るか、 repl 内で定義しておき、  
(filter-autotest) か (all-autotest) かを評価する。  
そうすると、 filter-autotest の場合は filter つきだけが、  
all-autotest の場合は全てが実行される。  

## 注意点
{% highlight clojure %}
(fact "do something"
 (fact "do2"
  ....
{% endhighlight %}
のように、 fact でネストした場合は filter に引っかからないので、  
この場合は 最初の fact を fact-group に修正してあげましょう。  

## 所感

これがわかってよかった。開発効率が ちょっと上がった。  
