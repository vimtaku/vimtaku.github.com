---
layout: post
title: "template test"
description: ""
category: "dev"
tags: [jekyll,blog,pygments]
---
{% include JB/setup %}

## code ハイライトテスト
jekyll pygments.rb は python2.7 じゃないとだめらしい。

{% highlight ruby %}
def foo
  puts 'foo'
end
{% endhighlight %}

{% highlight python %}
# coding: utf-8
import sys

from pygments import highlight
from pygments.lexers import guess_lexer
from pygments.formatters import HtmlFormatter
{% endhighlight %}

{% highlight bash %}
$ cd UserName.github.com
$ git init
$ git add .
$ git commit -m "first commit"
$ git remote add origin https://github.com/UserName/UserName.github.git
$ git push -u origin master
{% endhighlight %}


{% highlight vim %}
function! SetColumnWidthLimit()
  setlocal textwidth=78
  if exists('+colorcolumn')
    setlocal colorcolumn=+1
  endif
endfun

au Filetype perl,javascript,vim call SetColumnWidthLimit()
{% endhighlight %}

## 参考URL
http://web.sfc.keio.ac.jp/~t10078si/wpx/?p=862
