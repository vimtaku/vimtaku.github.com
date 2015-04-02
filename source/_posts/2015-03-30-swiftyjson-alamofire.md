---
layout: post
title: "Alamofire_SwiftyJSON でビルドエラーが出るとき"
description: "Alamofire_SwiftyJSON でビルドエラーが出るとき"
category: "swift"
tags: [swift]
keywords: "swift alamofire swiftyjson"
---

# こんなエラーが出ていた
The operation couldn’t be completed. No such file or directory.  

# pods の dependencies に alamofire と swiftyjson をたしてあげよう
たぶんそれで行けるはず。  

# 他のマシーンでなぜか動かない場合
[この stack overflow の](http://stackoverflow.com/questions/26834293/swift-could-not-build-objective-c-module-alamofire)
Dependencies を 削除する、でうまく行った。  
Clean で全部消えないとか、どんだけ罠なんだよ。  

