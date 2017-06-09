---
layout: post
title: "react_native_fcm_undeclaired_identifier"
date: 2017-06-09 21:48:23 +0900
comments: true
categories: react-native
---

# I got these message when start to use react-native-fcm

```
error: use of undeclared identifier 'FIRMessagingConnectionStateChangedNotification'
   name:FIRMessagingConnectionStateChangedNotification object:nil];
```

I lot of time consumed, but I found that Firebase version is weired for me.

```
pod install --repo-update
```

save me.

that's it.
