---
layout: post
title: "web エンジニアのためのデータベース技術入門をざっくり読んだ"
description: "web エンジニアのためのデータベース技術入門をざっくり読んだ"
category: "mysql"
tags: ["mysql"]
---

## web エンジニアのためのデータベース技術入門をざっくり読んだ

[この人の感想ブログがいい感じ](http://hamasyou.com/blog/2012/05/09/4774150207/)。  
なので、自分で気になったあたりを調べたところとかを、以下にメモしていく。  

### Sharding と更新性能
RAID を組んだ マスタの場合、冗長性だけでなく並列性においても有利である。  
MySQLではマスタはマルチスレッドで動作するが、  
スレーブはシングルスレッドで動いているから、スレーブのほうが負荷がかかりやすく、  
さらに raid のせいで I/O での差が発生する。  
なので、スレーブだけ SSD とか、SSD のなかでもさらに高性能な PCI-express SSD を使うと余裕になるっていう話。  

### MySQL の Hash JOIN について
MySQL では join とかするときは、残念なことに MySQL5.6 でも Nested Loop に変換されてしまう。  
Hash JOIN のほうがすごく早い場合がある、が、単純に Hash Join のほうが早いというわけではない。  
 [mysql performance blog](http://www.mysqlperformanceblog.com/2012/05/31/a-case-for-mariadbs-hash-joins/)の結論部をたいして無い英語力で翻訳してみると、  

{% blockquote %}
Based on the above information and the benchmark results for different test cases, we can see where Hash Joins work best and where they don’t. First of all Hash joins only work for equijoins. Hash join work best when you are joining very big tables with no WHERE clause, or a WHERE clause on a non-indexed column. They also provide big improvement in query response time when you are joining tables with no indexes on the join condition (Full Join). The best performance with Hash Join can be achieved when the left table can fit completely in the join buffer, or when the least amount of buffer refills are needed, as each buffer refill means a scan of the right-side table. However, Hash joins do not outperform BNL or BKA when you are joining a really small subset of rows, as then scanning the right-side table becomes costly in comparison. Block Nested Loop Join would perform better than Hash Join when you are joining two tables on a PK column such that both tables are read in PK order. One use case that I can think of for hash joins is data warehouse applications that need to run reporting queries that need to join on lookup tables which tend to be small mostly. What use cases can you think of
{% endblockquote %}

{% blockquote %}
まず Hash join は inner join じゃなきゃそもそもうごかない。  
Hash join は where 句なしの大きいテーブルか、  
where 使っているけど インデックスはられていないカラムの条件指定の時に効果を発揮するよ。  
もしくは、index がはられていない列同士の結合とかにもだいぶ効果発揮するよ。  
ベストパフォーマンス発揮するタイミングは、左のテーブルがジョインバッファに全部収まる  
(最低限再度バッファに入れる時に入るサイズ)ときだ。  
それは右のテーブルを読みきった時にまたジョインバッファ更新するから。  
すげぇ小さいテーブルとか、右のテーブルに const 検索条件が使われている時とかは BNL とか BKA とかのほうがいい感じだぜ。  
BNL は PK での 検索条件とか、 PK での sort order 使ってるときに Hash join よりいいぜ。  
まぁ、一つ思いつくのは data ware hause Application とかでレポートする処理とか書く時にかなり早くなりそうだよね。  
あとなんかある？  
{% endblockquote %}

って感じだ。  
なるほど、そう考えると格段と良くなるとは言えないが、場合によっては相当効力がありそうだ。  
しかもグラフを見ると、なるほど、たしかに、そうとうパフォーマンスが上がっている場所がある。  
というか mysql 5.5 と 5.6 の差が違いすぎてウケる。  

#### 参考:
[http://d.hatena.ne.jp/interdb/20131020/1382280437](http://d.hatena.ne.jp/interdb/20131020/1382280437)  
[http://www.mysqlperformanceblog.com/2012/05/31/a-case-for-mariadbs-hash-joins/](http://www.mysqlperformanceblog.com/2012/05/31/a-case-for-mariadbs-hash-joins/)  

### スレッドプールについて
MariaDB だとスレッドプールが使用できるが、MySQL 5.6 ではその機能はない。  
エンタープライズ版とかだと用意されているらしい。 ちなみに MySQL6 とかで実装されるらしい。  

### MySQL チューニングについて
これらの記事がおそらくとても参考になるので、本番運用前にチェックしてみるとよいかも。  
[http://yakst.com/ja/posts/200](http://yakst.com/ja/posts/200)  
[http://nippondanji.blogspot.jp/2009/03/mysql7.html](http://nippondanji.blogspot.jp/2009/03/mysql7.html)  
[http://dsas.blog.klab.org/archives/50860867.html](http://dsas.blog.klab.org/archives/50860867.html)  
[http://www.slideshare.net/kenmasu/ss-12604339](http://www.slideshare.net/kenmasu/ss-12604339)  

以下メモ  
show engine innodb status;  
で ロックが発生していないか見る。  

これで遅いトランザクションの洗い出しが可能。  
https://github.com/yoshinorim/MySlowTranCapture  

この辺は見直すとよいかも。
{% highlight sql %}
mysql> show global variables like '%innodb_lock_wait_timeout%';
{% endhighlight %}


### RDS におけるリストアについて
オンプレミスな DB のリストアなら、  
ある地点のスナップショットのリストア + バイナリログをロールフォワードだと思うけど  
RDS ならどうするんだろうと思ってちょっと調べたけど、最初から MultiAZ にしておけば  
自動フェイルオーバーしてくれる模様。  
しかし3-5分くらいかかる模様なので、その間システムが落ちるのかと思うと結構しんどい。  
[手動でフェイルオーバー](http://ijin.github.io/blog/2013/05/21/custom-non-rds-multi-az-mysql-replication/)
とかもあってこっちは復旧はすごく早いけどこれも結構デメリットはあるみたい。  
何を取るかだとは思うけど(おそらく組織レベルでサービスの停止が許せるかどうか)、  
メンテの楽さを考えると RDS 任せのほうが楽な気はする。  

### 所感
この本に関しては、業務でやっている、やっていたことが結構書かれていた。  
無意識レベルでやっていたことが結構書かれていたのでそういうところは飛ばして読んだ。  
でも、mysql のチューニングのあたりなどはあまりやったことがない経験だった。
前職では運用チームの人たちがいたのでその人達で閉じていた知識だと思うので、  
今の職場で活かせるようにチューニングしたいと思った。  
  
Wikipedia や Google は MariaDB を採用しているらしい。  
まだ枯れているという印象は全然ないけど、実際に使われていることや、ほぼ MySQL だったりするところ、  
スレッドプールが使えるあたりはかなり変わってきそう。  
hash join も使えるので、オプティマイザが賢ければかなりその恩恵受けられそう。  
[オプティマイザ比較しているブログ](http://d.hatena.ne.jp/interdb/20131020/1382280437)
見る限り、実は postgresql が頑張っている。  
postgresql は前前職で使っていたが vacuum の印象が強くてあんまりいいイメージはない。  
今度自分のプロダクトを作るときには MariaDB を使ってみようと思う。  

### 参考
* [http://dba.stackexchange.com/questions/43439/is-there-any-way-to-force-mysql-use-hash-join-instead-of-nested-loop-join](http://dba.stackexchange.com/questions/43439/is-there-any-way-to-force-mysql-use-hash-join-instead-of-nested-loop-join)
* [http://nippondanji.blogspot.jp/2009/03/mysql7.html](http://nippondanji.blogspot.jp/2009/03/mysql7.html)  
* [http://www.atmarkit.co.jp/ait/articles/0503/24/news107_2.html](http://www.atmarkit.co.jp/ait/articles/0503/24/news107_2.html)
