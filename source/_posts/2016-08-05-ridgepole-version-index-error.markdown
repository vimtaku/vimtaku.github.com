---
layout: post
title: "ridgepole command gets [ERROR] undefined method index"
date: 2016-08-05 16:53:16 +0900
comments: true
category: "ruby"
tags: ["ridgepole", "ruby", "rails"]
---


## TL;DL
You should use newest version of gem definetly.

## I faced this error
```
[ERROR] undefined method `index' for #<Ridgepole::DSLParser::Context::TableDefinition:0x007fae9aa1b850>
```

## Solved
This is version problem.

I simply write as follows,

```
gem 'ridgepole'
```

but it seems downloads version is 0.5.0.

I finally found 0.6.4 is newest ver for now, so I used it, problem disappeared.
