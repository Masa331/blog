---
layout: post
title:  "Preconnect for performance"
date:   2015-11-02 21:22:49
comments: true
author: Přemysl Donát
---
# Preconnect and reap the low hanging performance fruit

All credits to this post [Eliminating Roundtrips with Preconnect](https://www.igvita.com/2015/08/17/eliminating-roundtrips-with-preconnect/) but this preconnect thing is really cool!!

Ok, this is how a fresh request to my homepage looks in terms of performance:


![homepage performance without preconnect]({{site.url}}/assets/images/homepage-performance-without-preconnect.png)

See these 93ms? That's time it takes my browser to initiate connection with font awesome CDN.

And look at this:

![homepage performance with preconnect]({{site.url}}/assets/images/homepage-performance-with-preconnect.png)

No 93ms for connection initiation! **One fifth of the total time saved**. And only thing you need to do is this:

{% highlight html %}
  <link rel="preconnect" href="https://maxcdn.bootstrapcdn.com" crossorigin>
{% endhighlight %}

With this link you are telling browser you might pull something from bootstrapcdn.com. As soon as browser gets this link it will initiate a new connection instead of doing it after some CSS requests it. Once CSS requests it, connection is already prepared and that saves a lot of time.

**Read the original article and it's own sources for more in-depth explanation:**
[Eliminating Roundtrips with Preconnect](https://www.igvita.com/2015/08/17/eliminating-roundtrips-with-preconnect/)

On SupTasks implemented in commit:
[d4ddf32](https://github.com/Masa331/suptasks/commit/d4ddf3223395a78706042074f5788666f9c8d5b8)
