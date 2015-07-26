---
layout: post
title:  "Enumerator and iterating really"
date:   2015-07-25 21:22:49
author: Přemysl Donát
---
# Ruby Enumerator and iterating

So there is this `Enumerator`. I'v heard about it but to be honest, i don't really know what it is. In my journey to get to know him i had to stop and learn multiple other things too.

Let's see!

## Iterators

Iterator is programming construct which helps you to cycle through some list or array and do something with it's contents.

Complete here:
[https://en.wikipedia.org/wiki/Iterator](https://en.wikipedia.org/wiki/Iterator)

Iterators can be divided into two types

### 1) Internal iterator
This iterator is build right in the collection itself. And you can use it in objective manner:


{% gist ff1e915a8f6f12f9b97d %}

~~~
PrimeNumbers.each do { |number| puts number }
=> 1
=> 2
=> ...
~~~

### 2) External iterator

External iterator is build independently from the collection it iterates. It can be function, object...

{% gist 77bda2d4e2234a8c5c4f %}

~~~
iterator = UnderFiveIterator.new(PrimeNumbers)
iterator.each_under_five { |number| puts number }
=> 1
=> 2
=> 3
~~~

As you can see `UnderFiveIterator` internally uses another iterator to perform it's own iteration, but that's all right.

## Enumerator

Ok, probably everybody heard of `Enumerator` in ruby. But do you really know what it is? I'v started programming before 2 years and i didn't yet come to a situation when i would really need to know. But i'm really curious, so let's investigate:

Basiclly, as i understand it, enumerator provides 3 things. First it provides data for enumeration. Second, it defers the teration until moments it's really needed. Third, it let's you control the iteration, if you want to.
Basically enumerator is object, which wraps the whoel iteartion operation.
