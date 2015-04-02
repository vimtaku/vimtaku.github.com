---
layout: post
title: "rails_admin の日付のピッカーで日本語が使われて argument out of range エラーが出るときの解決法"
description: "rails_admin の日付のピッカーで日本語が使われて argument out of range エラーが出るときの解決法"
category: "rails"
tags: ["rails"]
---

# 概要
rails_admin の日付のピッカーで日本語が使われて argument out of range エラーが出る。  
原因は
[参考のQiita](http://qiita.com/kuboon/items/1d009e2f89729fe5db78) 参照。  
  
自分の場合はうまくいかなかったので、ソースおったらここ直せば良さそうだったので  
ここを上書きする感じにした。  

#### config/initializers/rails_admin.rb
```
# Fix for bug when specified japanese datetime string
# http://qiita.com/kuboon/items/1d009e2f89729fe5db78
module RailsAdmin
  module Config
    module Fields
      module Types
        class Datetime < RailsAdmin::Config::Fields::Base
          def localized_date_format
            "%Y-%m-%d"
          end
        end
      end
    end
  end
end
```

以上。  
