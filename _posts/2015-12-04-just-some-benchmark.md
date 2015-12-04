---
layout: post
title:  "Just some string benchmark"
date:   2015-12-04 21:22:49
comments: true
author: Přemysl Donát
---
# Just some string benchmark

Been thinking how [#delete](http://ruby-doc.org/core-2.2.3/String.html#method-i-delete) [#tr](http://ruby-doc.org/core-2.2.3/String.html#method-i-tr) [#gsub](http://ruby-doc.org/core-2.2.3/String.html#method-i-gsub) methods are fast:

{% highlight ruby %}
'some@email.com'.delete('@.')
'some@email.com'.tr('@.', '')
'some@email.com'.gsub('@', '').gsub('.', '')
{% endhighlight %}

## Result

| method | real |
|--------------|--------------------|
| #delete | (0.008266) |
| #tr | (0.009554) |
| #gsub | (0.026041) |

The results will be different on other machine but it looks like `#delete` is fastest.

## Code used for benchmark

{% highlight ruby %}
require 'benchmark'

Benchmark.bmbm do |x|
   x.report { 10000.times { 'donat@gmail.com'.delete('@.') } }
   x.report { 10000.times { 'donat@gmail.com'.tr('@.', '') } }
   x.report { 10000.times { 'donat@gmail.com'.gsub('@', '').gsub('.', '') } }
end
{% endhighlight %}
