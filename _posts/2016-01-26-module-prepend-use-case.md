---
layout: post
title:  "Module#prepend with practical example"
date:   2016-01-26 21:22:49
comments: true
author: Přemysl Donát
---
# Module#prepend with practical example

`Module#prepend` is a feature added in Ruby 2.0. Until now i didn't have an oportunity to try it. Turns out it's pretty cool and useful!

## Some background on method calling

When you call a method, Ruby looks on the receiver's ancestor chain from left to right and invokes method from the first member which implements it.

If you call `super` keyword inside a method Ruby invokes same method on next member which implements it. Unfortunately, there is no way how to call same method on previous member.

Now this is how ancestor chain might look in Ruby 2.0:

*[object's singleton class, **prepended modules**, object's class itself, included modules, superclasses]*

When you `prepend` a module it is actually inserted **before** the class itself in ancestor chain, not after as it's with `include`. Thus when you define same method in the prepended module and the class and then you call it **the method from prepended module is actually invoked**.

Demonstration:
<script type="text/javascript" src="https://asciinema.org/a/34831.js" id="asciicast-34831" async></script>

Before Ruby 2.0 this wouldn't be possible.

## Practical example

I have few service classes which all have same structure. These classes serve as simple data modifiers. It's something like this:

{% highlight ruby %}
module Contact
  class PostalCodeNormalizer
    attr_accessor :raw, :modified, :invalid

    def intialize(raw_contacts)
      @raw = raw_contacts
      @modified = []
      @errors = []
    end

    def call
      raw.each do |contact_info|
        if contact_info[:postal_code].blank?
          errors << [contact_info, 'Postal code is missing!']
        else
          contact_info[:postal_code].delete(' ')

          modified << contact_info
        end
      end

      if raw.size == (modified.size + errors.size)
        [modified, errors]
      else
        fail "sums aren't same"
      end
    end
  end
end
{% endhighlight %}

I have a bunch of these with different functions. I chain them as i need, collect the modified data and errors and do something with them. But the size checking code at the end of `#call` method is always the same.

With `Module#prepend` it can be very nicely refactored:

{% highlight ruby %}
module Contact
  module BaseModifier
    ...

    def call
      super

      if raw.size == (modified.size + errors.size)
        [modified, errors]
      else
        fail "sums aren't same"
      end
    end
  end

  class PostalCodeNormalizer
    prepend BaseModifier

    def call
      raw.each do |contact_info|
        if contact_info[:postal_code].blank?
          errors << [contact_info, 'Postal code is missing!']
        else
          contact_info[:postal_code].delete(' ')

          modified << contact_info
        end
      end
    end
  end
end
{% endhighlight %}


Before `Module#prepend` i would have to do something like this:

{% highlight ruby %}
module Contact
  module BaseModifier
    ...

    def call
      do_call
      ...
    end

    def do_call
      fail 'implement in subclass'
    end
  end

  class PostalCodeNormalizer
    include BaseModifier

    def do_call
      ...
    end
  end
end
{% endhighlight %}

Or something like this:
[Module.prepend: a super story](https://hashrocket.com/blog/posts/module-prepend-a-super-story)

Have a good one!
