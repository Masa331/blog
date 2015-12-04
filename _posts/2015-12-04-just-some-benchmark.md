---
layout: post
title:  "Just some string benchmark"
date:   2015-12-04 21:22:49
comments: true
author: Přemysl Donát
---
# Just some string benchmark

Been thinking how [#tr](http://ruby-doc.org/core-2.2.3/String.html#method-i-tr) [#delete](http://ruby-doc.org/core-2.2.3/String.html#method-i-delete) [#gsub](http://ruby-doc.org/core-2.2.3/String.html#method-i-gsub) methods are fast:

{% highlight ruby %}
'some@email.com'.tr('@.', '')
'some@email.com'.delete('@').delete('.')
'some@email.com'.gsub('@', '').gsub('.', '')
{% endhighlight %}

## Result

| method | real |
|--------------|--------------------|
| #tr | (0.009292) |
| #delete | (0.014222) |
| #gsub | (0.024745) |

The results will be different on other machine but it looks like `#tr` is fastest.

## Code used for benchmark

{% highlight ruby %}
require 'benchmark'

Benchmark.bmbm do |x|
   x.report { 10000.times { 'donat@gmail.com'.tr('@.', '') } }
   x.report { 10000.times { 'donat@gmail.com'.delete('@').delete('.') } }
   x.report { 10000.times { 'donat@gmail.com'.gsub('@', '').gsub('.', '') } }
end
{% endhighlight %}
