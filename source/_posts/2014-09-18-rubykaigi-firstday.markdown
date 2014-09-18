---
layout: post
title: "rubykaigi 2014/ 1日目"
description: "rubykaigi 2014/ 1日目"
category: "ruby"
tags: ["ruby", "rubykaigi"]
---

# RubyKaigi first day

とりあえずまとめてみた。  

## [(key note) Building the Ruby interpreter -- What is easy and what is difficult?](http://rubykaigi.org/2014/presentation/S-KoichiSasada)

何が簡単で、何が難しいのかという話。  

### メモ

#### パフォーマンス
パフォーマンスを単純に上げるのは簡単だが、メンテし易さなどとトレードオフの関係にあるので、  
メンテしやすく、さらにパフォーマンスを上げるのは簡単ではない。  
  
#### VM
VM や JIT コンパイルの仕組みなどを作るのはそんなに難しくない。  
しかし reliability を保ちながら作るのは難しい。  
  
#### メンテしやすさ
Ruby の実装を Ruby で実装すればわかりやすいしメンテしやすい。  
ruby のコードをいじったあとでは、 テストとか全部通ればまだ良い方(ダメだったら全部通らないから)。  
  
#### 並列性
単純にカーネルレベルスレッドの仕組みを作るのは簡単だが、 ruby のユーザに残念体験を与えないためにあえてそうしてない。  
また、スレッドを適切に扱える技術者(ウィザード)は数少ない。という説明もされていた。  
同時実行性があり、バグの再現実効性が低いのも大変である。  

#### GCアルゴリズム
ライトバリアを実装するのは簡単だが、拡張ライブラリなどにも実装するのは大変。  

#### 性能評価
仮想のサーバでは性能評価が難しいので、物理サーバが必要。  
実行時間とはなにか。メモリ使用量とは結局なんだろう(平均?最大?)。  

#### コミッターになるには
ただコミッターになるのは、matzにコミッター権限貰えばよい。  
しかし、デベロッパーとして参加するのは大変。  

#### Ruby(処理系)をもっと知るために
Rubyソースコード完全解説  
[http://i.loveruby.net/ja/rhg/book/](http://i.loveruby.net/ja/rhg/book/)  
Ruby Under a microscope  
[http://shop.oreilly.com/product/9781593275273.do](http://shop.oreilly.com/product/9781593275273.do)  
(日本語翻訳中らしい)  

#### もっと良い技術者になるために
新しい技術を調べて  
 - ブログを書いて  
 - 議論をし  
 - sns でチャットして  
 - 会議に参加して  
 - カンファレンスで発表  
していきましょう。

### 所感
さすが ruby コミッターという凄さがあった。  
普段 GC とかあまり意識しないけど、知識としてきちんと知っておかなきゃダメだなと思った。  


## [Controller Testing: You’re Doing It Wrong](http://rubykaigi.org/2014/presentation/S-JonathanMukai-Heidt)

やりかた間違ってるよ的な煽りタイトル。

### メモ

#### ダメなことあるある
 - いろんなことをテストしようとしちゃっている  
 - 全部スタブしちゃってる  

#### コントローラのテストでやるべきこと
 - authorization  
 - リソースの存在  
 - レスポンスの形式  

#### 命令的ではなく、宣言的に
 - チェックリストのようにテストを書こう  

#### 実際問題
 - 5/6 の、今年やったプロジェクトでは、コントローラベタ書きｗｗｗ

#### なぜファットモデルのほうが良いのか
 - ActiveModel で作っておくと良い  
 - すごく簡単なロジックでもコントローラに書いていて, それが変更による変更.. となるとすぐ太ってしまう。  
 -- モデルにしておくほうが良い  

#### まとめ
 - テストは宣言的に、シンプルにかけ  
 - コントローラにロジックかくな、モデルにかけ  

### 所感
英語のセッションだったけどそこそこ聞き取りやすい英語でよかった。  
それでも完全に内容を把握できたわけじゃあないけど。  

## [Continuous Delivery at GitHub](http://rubykaigi.org/2014/presentation/S-RobertSanheim)

ギッハブ社で行っている継続的デリバリについて  
(普段会社で行っていることはメモってなかった)

### メモ

#### feature flags
ある機能を入れるときには、  
{% highlight ruby %}
def some_new_feature_enable?
  something and user.is_stuff?
end
{% endhighlight %}
的なものを書いて、それが良さそうなら true に書き換えるなどして切り替えできるようにしていた。  
(そういえば前職でもそういう仕組みがあった。)  

#### ブランチコントロールの話
 - 長いことブランチを活かしておくと臭ってくるやん？  
 - たとえば rails2 から rails3 に上げるみたいなプロジェクトがあった(実際に)。
 -- RAILS3=true てきな環境変数で rails3? みたいなコードを書いて条件分岐して master につけていった。

#### フィードバック可視化
ちゃんとしててすごく良かった

### 所感
機能をフラグで管理する( redis とか使ってもいいかも) のは確かに良い物かもなぁと思った。  
ただ、ゆうてもこういう仕組みはものすごく大きい変更に関してはやっていけない気もした。  
ブランチコントロールの話は、twitter では、うちは無理だ、とか出来そうにないとか、怖いとか  
そういう反応が散見された。  
確かに怖いは怖いし、実際、まとまった機能をつける場合は,  
mediuamfeature/master みたいに master からきって、  
mediuamfeature/feature_branch で mediuamfeature/master に pull req だしつつ  
ある程度育ったら mediuamfeature/master で master を pull rebase で差分を取り込む感じでやっていた。  
これは議論の余地はあるなぁ。  

## [What's wrong with your app?](http://rubykaigi.org/2014/presentation/S-KeikoOda)
これも割りと釣りタイトルっぽくて聞いたけど、内容は heroku チームと、heroku 使ってるヒトの H12 とかいう  
エラーの原因がなんなのか？みたいな話だった。  
heroku に特化した感じであんまりだったので省略。  


## [Non-Linear Pattern Matching against Unfree Data Types in Ruby (Egison Pattern Matching in Ruby) ](http://rubykaigi.org/2014/presentation/S-SatoshiEgi)

egison っていうパターンマッチのプログラミング言語と、その ruby gem の話。  
あんまり直接メモはしてなかった。

### メモ
 - 様々なパターンマッチがあって、圧倒的な少ない記述量で、たとえばポーカーの役判定などができる。  
 - 麻雀の役判定も！(これはすごいことだ!!!!)  
 -- 複数の判定もできるとのことなので工夫すれば全部役を本当にだせる？  
 - 今注目されているプログラミング言語にも選出  
 - 東京大学のカリキュラムにも!  

### 所感
めっちゃすごい。  
通常のコードでも綺麗に簡潔に書けそうだ。  
とりあえず試しに使ってみると良いかも。  
すげぇどうでもいいけどググらビリティ低すぎる。  


## [Hypermedia: the Missing Element to Building Adaptable Web APIs in Rails](http://rubykaigi.org/2014/presentation/S-ToruKawamura)

とりあえずこの日一番ヒットというか、一番驚いたのがこのセッション。

### メモ

#### API の変更
変更マジしんどい。  
/v1/hogehoge を /v2/hogehoge とかにすると必ず api の変更が必要

#### FizzBuzzaaS の話
fizzbuzz を返す api サーバ。  
100までの数値に対応している。  

この api にテストを書こうとすると  
/fizzbuzz/endpoint/1  
/fizzbuzz/endpoint/2  
/fizzbuzz/endpoint/100  
...  
となって、/fizzbuzz/endpoint のところはハードコードだし、  
最後の値は数値を for とかで回すことになる。  
それを、最初のレスポンスが nextUrl 的に、次の値を渡してくれれば  
iterator で問題なくテストできる(当然最後もわかるし、100が変わっても大丈夫)。  

#### そこで hypermedia
HTML もそうだけど、次へのリンクや、前へのリンクなどはちゃんと付いている！  
ブラウザのバージョンがかわっても全然大丈夫！  

#### json + link などの情報フォーマット
じつはそういう規格がある。
Uber, HAL, JSON-LD など..

#### HTML で web の API が表現可能!
それで作ったのが[これ](https://github.com/tkawa/hypermicrodata)

#### 使い方としては
設計順かいてあったけど、メモれてない..  
状態遷移を書く、はすごく重要だと説明されていた。  

#### まとめ
[ここの qiita でほぼ書いてある](http://qiita.com/tkawa/items/0efd49ad07d39531a520)

### 所感
正直言ってこの発想はなかった！と思った。  
会場も、なるほどそうきたか！みたいな感じだった。  
ただ、実際つかっている HTML の構造変えたくなったりしないのかなぁとか、  
疑問はあったりする。  
あ、というか api 用の HTML(view) を書いておけばいいのかな。  
まぁ一つの手段としてこれはすごくあるなぁと思った。  
API から web を作るのは、すごく手間ではないけど、二度手間であることに変わりはないし。  


## ["Gem of this Week" - building culture and making gem](http://rubykaigi.org/2014/presentation/S-TakumiMiura)
ドリコムのヒトの話。  
gem 作って共有する文化づくりのための工夫のお話。  
今週のgem(媒体がチャットなのかメールなのかは不明)を作って、 gem を作る週間を作った。  
社内用の rubygems 的なものを作った (drecom gem)。  
これで gem publish みたいなことができた。  
ヒアリングして不安を潰すことでそういう文化を作った、っていう話だった。  


## 最後に
すごくいろいろいい話をきいた。  
どうでもいいけど、たったこれだけまとめるのに 2時間くらいかかってる。  
