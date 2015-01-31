---
layout: post
title: "Swift の promise ライブラリの BrightFuture と Craft を使ってみたら Craft のほうが良かった"
description: "Swift の promise ライブラリの BrightFuture と Craft を使ってみたら Craft のほうが良かった"
category: "swift"
tags: [swift, promise, craft]
keywords: "swift, promise, craft"
---

{% include JB/setup %}

# TOC

# はじめに
最近 Swift でアプリを書いているんだけど、 promise のライブラリをどうしても使いたくて色々見ていたら、  
なんか BrightFuture っていうやつが Star が多いように見えて  
しばらくつかってみたけど、then でつなげた結果を次に返すみたいなのができそうになかった。  
Craft は js の promise と同じように使えて、 chainable なので便利だった。  

# Craft 使用例

```
    let moge = "moge"
    let promise = Craft.promise({
      (resolve: (value: Value) -> (), reject: (value: Value) -> ()) -> () in

        // post の処理
        Alamofire.request(Router.Ticket).responseObject { (request, response, ticket:Ticket?, error) in
            if (ticket != nil) {
                resolve(value: ticket!.token!)
            } else {
                reject(value: NSError(domain:"error occured", code:404, userInfo:nil))
            }
        }
    })
    promise.then({(value: Value) -> Value in

        println("promise 1 value 1 is ")
        println(value) ; 1コメのやつ

        let promise = Craft.promise({(resolve: (value: Value) -> (), reject: (value: Value) -> ()) -> () in
            Alamofire.request(Router.Ticket).responseObject { (request, response, ticket:Ticket?, error) in
                if (ticket != nil) {
                    resolve(value: "2komenoyatu!!!!!!!!!!!!")
                } else {
                    reject(value: NSError(domain:"error occured", code:404, userInfo:nil))
                }
            }
        })
        return promise
    }).then({(value: Value) -> Value in
        println("promise 2 value 2 is ")
        println(value) ; 2コメのやつ
        return "mogo"
    })
```

# 幾つか気づいた店
 - promise.then のところは続けて書かないとダメ。つまり( ).then({ ... っていう感じ。  
 - swift のコンパイラがダメなのかわからないけど、 responseObject がねぇよって言われてて、  
 意味分かんないから println とか入れるとコンパイラが通る。 甚だ謎。  

# 所感
やっと promise で楽に書けそう。  

# 参考URL
[https://github.com/Thomvis/BrightFutures](https://github.com/Thomvis/BrightFutures)  
[https://github.com/supertommy/craft](https://github.com/supertommy/craft)  



