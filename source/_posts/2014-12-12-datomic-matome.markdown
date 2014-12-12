---
layout: post
title: "Datomic まとめ"
description: "Datomic まとめ"
category: "datomic"
tags: [datomic, clojure]
keywords: "datomic, clojure"
---

{% include JB/setup %}

## TOC
* 
{:toc}

# datomic まとめ(追記していく)

## datomic 自体の説明
省略。  
いつか書くかも。
他に良い記事がたくさんあるので。  


# datomic 本当のところ(使いながら追記していく)

## created_at, updated_at は持つ必要がない
rails で mysql とかつかって作るときだと当たり前のように created_at とか updated_at を持っているけど、  
datomic を使う場合は必要ない。  
stack over flow に、持ってた方が良い？などの質問に対して、
おれは created_at 入れているみたいな意見があったけど、別にいらないと思う。  

{% highlight clojure %}

; -------- 一応コピペしとく
(def db-url (ref ""))
(dosync
  (ref-set db-url  "datomic:free://localhost:4334/hoge"))

(defn setup-db [db-url]
  (d/create-database db-url)
  (d/transact
    (d/connect db-url)
    (concat
      (ds/generate-parts d/tempid (dbparts))
      (ds/generate-schema d/tempid (dbschema)))))
(defn setup-testdb []
  (dosync
    (ref-set db-url (str "datomic:mem:" (d/squuid))))
  (setup-db @db-url)
)

(defn connect-db []
  (d/connect @db-url)
)

; -------- ここが本質
(defn history [eid]
  (d/q
    '[:find ?e ?a ?v ?tx ?added
      :in $ ?e
      :where
      [?e ?a ?v ?tx ?added]]
    (d/history (d/db (connect-db)))
    eid
    )
)
{% endhighlight %}

これで帰ってくる値は [entity_id, attribute_id, value, transaction_id, added?]  
で、これらが引けるということは created_at, updated_at をあえて持たなくて良い。  

{% blockquote %}
#<HashSet [
 [17592186045436 74 this is new emotion. 13194139534331 true],
 [17592186045436 73 0 13194139534331 true],
 [17592186045436 72 17592186045423 13194139534331 true],
 [17592186045436 71 17592186045421 13194139534331 true],
 [17592186045436 75 17592186045432 13194139534331 true],
 [17592186045436 70 #inst "2014-12-12T00:11:51.228-00:00" 13194139534331 true],
 [17592186045436 71 17592186045422 13194139534333 true],
 [17592186045436 71 17592186045421 13194139534333 false],
 [17592186045436 74 hogera! 13194139534333 true],
 [17592186045436 74 this is new emotion. 13194139534333 false]
]>
{% endblockquote %}
サンプルをはるとこんな感じで帰ってきている。(手動で sort 済み)  
これは 13194139534331 と 13194139534333 のトランザクションでインサートされたこと、  
71, 74 の属性が 変更されたことなどが読み取れる。  


## 参考資料(便利)
- [http://sunday-programming.hatenablog.com/category/datomic](http://sunday-programming.hatenablog.com/category/datomic)  
-- めっちゃ参考にさせていただいております。  
- [http://www.learndatalogtoday.org/](http://www.learndatalogtoday.org/)  
-- datomic の query language である datalog のチュートリアル。datomic をやる前にこれを触っておくのオススメ。  
- [https://github.com/Datomic/day-of-datomic](https://github.com/Datomic/day-of-datomic)  
- [https://github.com/Yuppiechef/datomic-schema](https://github.com/Yuppiechef/datomic-schema)  
-- datomic のスキーマを書くのマジしんどいけどこれがあればいい感じでつくってくれて嬉しいやつ。  

