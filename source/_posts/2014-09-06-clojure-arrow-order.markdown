---
layout: post
title: "clojure の arrow マクロの実行順と逆順に実行されてハマった件"
description: "clojure の arrow マクロの実行順と逆順に実行されてハマった件"
category: "clojure"
tags: ["clojure", "compojure"]
---

わかっているひとは全然わかっていると思うんだけど初学者的にハマったので書きます。  

## 状況
compojure とかを使って

{% highlight lisp %}

このへんはしょうりゃく

(defroutes app-routes
  (GET "/" [] "Hello World")
  (route/resources "/")
  (route/not-found "Not Found"))


(defn wrap-json-request-params
   [handler]
     (print "wrap-json-request-params 11111111111")
       (fn [req]
            (print "inner wrap-json-request-params 111111111")
                (handler (assoc req :form-params (req :body)))
                  )
       )

(defn wrap-print-request
   [handler message]
     (print "wrap-print-request 222222222")

       (fn [req]
              (print "inner wrap-print-request 222222222")
                    (handler req)
                      )
       )

(def app
   (-> app-routes
     (wrap-json-request-params)
     (wrap-print-request "2222222")
   )
)

{% endhighlight %}

みたいに書いていた時、なんとなく、実行のイメージは
{% blockquote %}
(読み込み時)
"wrap-json-request-params 11111111111"
"wrap-print-request 222222222"

(実際にルーティングされた時)
"inner wrap-json-request-params 111111111"
"inner wrap-print-request 222222222"
{% endblockquote %}

だけど、実はこうならない。  
それは -> で渡しているから、  
wrap-json-request-params に第一引数で渡した結果  
つまり(print "inner wrap-json-request-params 111111111" を評価してから (handler req)する)closureがかえる。  
これを closure1とする。  
次の関数で、  
(wrap-print-request clojure1 "2222222") となって  
(print "inner wrap-print-request 222222222"してから (closure1 req) する closureがかえる。  
これを closure2とする。  
おそらく (closure2 req) のように request が渡ってくるので  
print "inner wrap print-request 222222222" してから、  
(closure1 req) が評価され  
print "inner wrap-json-request-params 111111111" が出力される。  
  
これだと、直感的に上から順に実行しているように思えなくなって辛いので、対処法として、  
内部の無名関数で最後に実行している (handler req) してるところを  
(let [moge (handler req)]  
 (println "inner wrap-json-request-params 111111111")  
 moge  
)  
などと書き換えて、最初に評価(let は宣言時に評価されるのを利用)してやれば、直感的にかける。  
ちなみに、複数こういうのをかますなら全部書き換えなきゃダメだと思う。  
逆に言うとこの辺で前後意識してかけるのは魅力かも。  
  
以上。  

