---
layout: post
title:  "Are you getting production error page in development?"
date:   2016-06-07 21:22:49
comments: true
author: Přemysl Donát
---
# Are you getting production error page in development?

I decided to internationalize one of my apps. Because i don't remember the structure in which i should add view translations(the ones starting with dots, eg.: `t('.greeting')`) i wanted to take advantage of rails internationalize errors which always tell you in which structure you should add the new translation:

![translation error]({{site.url}}/assets/images/translation_error.png)

But instead of the one above i got this:

![server error]({{site.url}}/assets/images/server_error.png)

WTF?! This should be shown only in production.

Then i did the following:

1. Quick check of environment config files. Nothing
2. Compared app config to other app behaving correctly. Nothign
3. Googled.. Nothing
4. Read Rails Guides. Nothing

And spend around 40 minutes on these.. :(

Ok, maybe time to read the error backtrace? :D

![backtrace]({{site.url}}/assets/images/backtrace.png)

Oh god, really?!:

![backtrace detail]({{site.url}}/assets/images/backtrace_detail.png)

Right there on top of it all. It's just i didn't read it

After i decided to internationalize, i created **empty** yaml `config/locales/cs.yml` file. Turns out it takes down whole Rails :)

Lesson learned? **Always read the fucking error message**. If only this didn't happen to me so many times already. And it isn't just me. I wonder why we always take the more frustrating way of googling instead of step back, read the error message and think about it a bit.
