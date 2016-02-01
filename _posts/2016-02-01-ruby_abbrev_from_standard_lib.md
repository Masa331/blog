---
layout: post
title:  "Abbrev from ruby Standard lib"
date:   2016-02-01 21:22:49
comments: true
author: Přemysl Donát
---
# Abbrev from ruby standard lib

Did you know about Ruby's standard lib [Abbrev](http://ruby-doc.org/stdlib-2.3.0/libdoc/abbrev/rdoc/Abbrev.html)? No? Well then let's take a look!

## What Abbrev does

For each string in an array `Abbrev` gives you all possible uniq abbreviations:

{% highlight ruby %}
require 'abbrev'

['table', 'wall'].abbrev
=> {"table"=>"table", "tabl"=>"table", "tab"=>"table", "ta"=>"table", "t"=>"table", "wall"=>"wall", "wal"=>"wall", "wa"=>"wall", "w"=>"wall"}
{% endhighlight %}

The [Implementation](https://github.com/ruby/ruby/blob/trunk/lib/abbrev.rb) is really simple and gives you 2 possibilities how to get your abbreviations. Either by `Abbrev::abbrev(words, pattern = nil)` or by calling `#abbrev` directly on any `Array`.

## What's it good for?

I think CLI's could use it. For example with Git's cli you can just call shortest possible abbreviation instead of original full command. And it saves a lot of keystrokes.

I can't find any other valid case yet. But for some reason i really like the library. So i was just playing a bit for now and created this gem [AbbreviatedMethods](https://github.com/Masa331/abbreviated_methods). With it you can call all your's objects methods by their's shortest possible abbreviations:

<script type="text/javascript" src="https://asciinema.org/a/35311.js" id="asciicast-35311" async></script>

I don't think it's really useful in production but definitelly interesting thing to play with. Have fun!

P.S.: If you find any use-case please let me know
