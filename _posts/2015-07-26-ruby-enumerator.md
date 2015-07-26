---
layout: post
title:  "What is Enumerator?"
date:   2015-07-26 21:22:49
author: Přemysl Donát
---
# What is Enumerator?

I started programming 2 years ago and i didn't yet come to a situation when i would really need to know. But i was always curious because words like 'Enumerator' and 'Enumerable' were present from the very beggining. So let's find out!

Long story short:

**Instances of `Enumerator` class are used to wrap iterations and let's you control the whole process.**


## Common usage
You can get an Enumerator like this:

{% gist 73e58c3aabc14e235883 %}

This Enumerator instance wraps iteration over `[1, 2, 3]`. We can start the actuall iteration with `#each` method. It yields all items to given block:

{% gist 5ad7525544205ea5203a %}

It behaves exactly same as `[1, 2, 3].each { |number| puts number }` call.

But you can also do this:

{% gist 4e405cd44d3adf0ffd0a %}

Like this you can manually iterate over the collection one item after another. `StopIteration` is raised when there are no more items. During the iteration, you can use `enum#peek`, `enum#rewind`, `enum#feed` or others, which greatly improves controllability. **With all these you can create your own iterators with custom logic.**

## Enumerator.new

You can also create Enumerator with `Enumerator.new { |yielder| ... }`.

Most simple example:

{% gist dbe29eeb87de8d82a798 %}

This time enum doesn't iterate over anything, but yields to given block **all values you explicitly yield with `yielder.yield something`**. In this example, only one value is yielded and it's integer 1.

To yield 1 ten times to given block so `enum.each { |item| puts item }` will print 1 ten times on screen:
{% gist 0800bd3f36c6c7c04b31 %}

And to yield infinetely:
{% gist 64f06bd6c61245c723e0 %}

If you call `#each` on this enum, it will never stop yielding and printing. But if you call `#next`, it will yield only once and then stop.

It's only the implementation of `#each` which calls `#next` in loop and goes on forever because `StopIteration`, is never raised.

**Internally enum stops execution every time `#yield` is called on yielder**.

Try it with putting print statements before and after yield:
{% gist a8353c161157f626717c %}

This technique is also used in example from official [documentation](http://ruby-doc.org/core-2.2.0/Enumerator.html), where infinite fibonacci sequence is generated.
