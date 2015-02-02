---
layout: post
title: "If you test using realm.io object, you should use RLMRealm.inMemoryRealmWithIdentifier"
description: "If you test using realm.io object, you should use RLMRealm.inMemoryRealmWithIdentifier"
category: "swift"
tags: ["swift", "realm"]
---

# TOC
* 
{:toc}

# In test, You should use inMemoryRealmWithIdentifier
If you test using realm.io object, you should use RLMRealm.inMemoryRealmWithIdentifier.  
I don't know why, but I got error many times when I wrote some unit test by using realm.  
The error is "swift_dynamicCastClassUnconditional".  
I struggled a lot, finally I got a solution.  
First of all, below is my code.  

  
```
    import Foundation

    class FMUserCredential: RLMObject {
        dynamic var accessToken :String = ""
        dynamic var refreshToken :String = ""
        
        // 代替案
        private struct ClassProperty {
            static var _realm:RLMRealm? = nil
        }
        
        // Computed Property
        class var _realm: RLMRealm? {
            get {
                return ClassProperty._realm
            }
            set {
                return ClassProperty._realm = newValue
            }
        }
        
        class func realm() -> RLMRealm {
            if (FMUserCredential._realm == nil) {
                let documentsPath = NSSearchPathForDirectoriesInDomains(.DocumentDirectory, .UserDomainMask, true)[0] as String
                let realmPath = documentsPath.stringByAppendingPathComponent("db25.realm")
                let realm = RLMRealm(path:realmPath, readOnly:false, error:nil)
                FMUserCredential._realm = realm
            }
            return FMUserCredential._realm!
        }
        
        class func findAll() -> RLMResults {
            return FMUserCredential.allObjectsInRealm(realm())
        }
        class func find() -> FMUserCredential? {
            if (findAll().count >= 1) {
                return findAll().firstObject() as FMUserCredential?
            }
            return nil
        }
    }
```


This class has static params realm, and when I test write, I set realm object.  
But, I notice that I have to new realm object when I do test.  
So, I changed my test code to use  

```
    let realm = RLMRealm.inMemoryRealmWithIdentifier("MyInMemoryRealm")
    FMUserCredential._realm = realm
```

This worked fine.  

## About realm
Realm is maybe so fast but I think this is quite difficult to use.  
Because I already had a lot of time to use it, actually I'm a cocoa beginner.  
The cost of studying it is quite high. but I don't know how difficult coredata is, so I cannot judge which one is better.  

## Related link
 - [http://realm.io/docs/cocoa/0.90.4/](http://realm.io/docs/cocoa/0.90.4/)
 - [http://stackoverflow.com/questions/24956956/swift-dynamic-cast-failed-with-http-get-request?rq=1](http://stackoverflow.com/questions/24956956/swift-dynamic-cast-failed-with-http-get-request?rq=1)
- [http://stackoverflow.com/questions/24841856/swift-breakpoint-in-coredata-library](http://stackoverflow.com/questions/24841856/swift-breakpoint-in-coredata-library)
