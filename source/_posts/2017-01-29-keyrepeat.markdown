---
layout: post
title: "Mac OS Sierra で Karabiner-elements がない場合に keyrepeat を早く適切にする方法"
date: 2017-01-29 11:15:34 +0900
comments: true
categories: ["karabiner", "karabiner-elements"]
---

## TL;DR

```
defaults write -g InitialKeyRepeat -int 9
defaults write -g KeyRepeat -int 2
```

## 背景
Mac OS Sierra にしてから karabiner elements になって、キーリピートの設定ができなくなった。
mac の system preferences からできる最速ではもちろん物足りないので、一旦目安としての数値がなかったので記述する。

## 概要
iterm から

```
defaults write -g InitialKeyRepeat -int 9
defaults write -g KeyRepeat -int 2
```

の各コマンドを実行して再起動したら反映された。若干はやいきもするが、慣れの問題かと思っている。

## 所感
最初1,1 とかにしたら大変なことになったので、誰かのためになればさいわいです。

