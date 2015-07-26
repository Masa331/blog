---
layout: post
title:  "How many symbols?"
date:   2015-07-22 21:22:49
author: Přemysl Donát
---
# How many symbols can you do?

So i'v been reading through Ruby 2.2 release notes and notice the new GC which also collects symbols.

This is long time known problem in Rails world. Prior to Ruby 2.2 symbols weren't garbage collected. So if you did somewhere create symbols from request params your app was vulnerable to DoS attacks. Google it if you want to know more.

I found one of my app vulnerable... :) so i wanted to try it of course. I wanted to see how does it look if your server crashes because of that.

It didn't work out for some reason. I created simple script which was doing requests with different param from which symbols were created. But the server was just fine. I guess the request-response cycle is really slow or something.

Well... it got me thinking. Do i really need to test it on Rails app? No! So i created simple ruby script:


{% gist 9da2e21c708a5fe2512b %}

Of course you have to run this on Ruby < 2.2.

After some time i realized it is really crappy metric because it depends on other processes using memory.

But it was really fun with this excersise. Learned a few things...

Note that at the end as your computer depletes all the memory and starts swapping the death by creating new symbols is really really slow. You can watch that in htop.

**But still... i can do about 27 000 000 symbols. What about you?**
