---
layout: post
title: "If you use ActiveRecord::Base.transaction syntax, you shoud surround it begin rescue clauses"
date: 2015-11-08 13:04:14 +0900
comments: true
categories: [rails, rspec]
---

# TL;DR
If you use ActiveRecord::Base.transaction syntax, you shoud surround it begin rescue clauses.


# Detail

```
class Hoge
  def self.exec
    raise "something is not good" if something_check

    ActiveRecord::Base.transaction do
      User.create(username:"moge")
      User.create(username:"moge")
    end

    rescue StandardError => e
      # if database transaction has occured, this statements will be called
      p "duplicate error"
  end
```

This code looks good, but includes terrible problem.
It's definetly rescue clause. In this code, something_check return true, raise "something is not good" error,  
`` and shows "duplicate error" ``. Terrible.

so if you use ActiveRecord::Base, I strongly recommend use begin rescue clauses implicit.

```
class Hoge
  def self.exec
    raise "something is not good" if something_check

    begin
      ActiveRecord::Base.transaction do
        User.create(username:"moge")
        User.create(username:"moge")
      end

      rescue StandardError => e
        # if database transaction has occured, this statements will be called
        p "duplicate error"
      end
    end

  end
```

