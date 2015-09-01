---
layout: post
title:  "Use semicolon in IRB"
date:   2015-08-08 21:22:49
comments: true
author: Přemysl Donát
---
# With semicolons in IRB or PRY no result printing happens

Semicolons aren't needed to end statements in Ruby. That's same with IRB or PRY. So i'v never tried it.

One day i accidentally wrote semicolon at the end of my PRY statement. What happend?:

{% gist 2c7eaef08b3039579de1 %}

Noticed how nothing was printed after second line? Normally you would get another `=> "Fred"` line. With semicolon at the end no printing happens.

## When it's actually usefull?

I work on a Rails project which uses PostgreSQL a lot. Sometimes when debugging i fire custom SQLs through ActiveRecord like this:

~~~
conn = ActiveRecord::Base.connection
conn.execute('select 1;')
~~~

The thing is when i assign connection into conn variable whole screen gets littered with connection info:

![ActiveRecord connection]({{site.url}}/assets/images/connection_angry_face.png)

With semicolon at the end nothing like this happens and i don't lose my context. :)

**Neat!**
