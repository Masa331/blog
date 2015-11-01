---
layout: post
title:  "Basic cookies security in rack"
date:   2015-10-28 21:22:49
comments: true
author: Přemysl Donát
---
# Basic cookies security in rack

I run my own Task & Time tracking app [SupTasks](https://suptasks.com). It's build on tiny ruby web framework [Roda](http://roda.jeremyevans.net/) which itself is build on rack. I was curious how sessions and cookies are handled in rack and in general so i'v been investigating it a bit lately.

Rack offers 3 built in session storage mechanisms. [Cookie](https://github.com/rack/rack/blob/master/lib/rack/session/cookie.rb), [Memcache](https://github.com/rack/rack/blob/master/lib/rack/session/memcache.rb) and [Pool](https://github.com/rack/rack/blob/master/lib/rack/session/pool.rb)

All of them use cookies to store session_id but after that they differ in where other parts of session are stored.

Most of us will use `Cookie` and it might be like this:

{% highlight ruby %}
use Rack::Session::Cookie
{% endhighlight %}

What's actually stored in cookie is base64 encoded marshalled hash:

![base64 marshalled hash]({{site.url}}/assets/images/cookie1.png)

Let's 'decrypt' it:
{% highlight ruby %}
require 'base64'

Marshal.load(Base64.decode64('BAh7CEkiD3Nlc3Npb25faWQGOgZFVEkiRWNjODdkMzEwMWZjNWY5ZWE3MDliMTgwNTg5NWQzNGFlMDU1ZTM4MGM0MzIwMmI4MDVjZjhlODcwOTk5ZGMyNTUGOwBGSSIPdXNlcl9lbWFpbAY7AEZJIhhzdXB0YXNrczFAZ21haWwuY29tBjsAVEkiDnVzZXJfbmFtZQY7AEZJIhJQcmVteXNsIERvbmF0BjsAVA=='))

=> {"session_id"=>"cc87d3101fc5f9ea709b1805895d34ae055e380c43202b805cf8e870999dc255",
 "user_email"=>"suptasks1@gmail.com",
 "user_name"=>"Premysl Donat"}
{% endhighlight %}

Wow! These are my credentials!

Ok, let's try something. I know my friend also has an account on SupTasks. And i know his e-mail:
{% highlight ruby %}
require 'base64'

Base64.encode64(Marshal.dump({"session_id"=>"c8ce1c99d9d33a9999fdc03e296777405f5e2d1f42a26eec4091fb96bcd738aa",
 "user_email"=>"notme341@gmail.com",
 "user_name"=>"Roman Postolka"}))

=> "BAh7CEkiD3Nlc3Npb25faWQGOgZFVEkiRWM4Y2UxYzk5ZDlkMzNhOTk5OWZkYzAzZTI5Njc3NzQwNWY1ZTJkMWY0MmEyNmVlYzQwOTFmYjk2YmNkNzM4YWEGOwBUSSIPdXNlcl9lbWFpbAY7AFRJIhdub3RtZTM0MUBnbWFpbC5jb20GOwBUSSIOdXNlcl9uYW1lBjsAVEkiE1JvbWFuIFBvc3RvbGthBjsAVA=="
{% endhighlight %}

If i replace cookie with this string i should be logged in as 'notme341@gmail.com':

![setting up a crafted cookie]({{site.url}}/assets/images/cookie2.png)

And voilà, after page reload, it works:

![logged in as different user]({{site.url}}/assets/images/cookie3.png)

## How to protect

Luckily, there is a simple fix for this:
{% highlight ruby %}
use Rack::Session::Cookie, { secret: 'some_long_random_secret' }
{% endhighlight %}

Now the cookie looks like this:

![base64 marshalled hash with appended digest]({{site.url}}/assets/images/cookie4.png)

This new cookie has two parts - first is our base64 encoded marshalled hash and second is it's SHA1 digest created with our new secret. Everytime session is loaded back from cookie, rack takes the first part and creates new digest from it. Then it's compared to the one stored in second part of cookie. If they match it means that noone manipulated with the cookie. If someone did, our new digest would be different from the saved one.

The most important thing now is to **save the secret somewhere safe**, so nobody can read it. Otherwise attacker could easily create digest which would match the rack check. You can keep your secret in server environment or load it from some yaml file but **never keep it in your version control**.

By the way in future versions of Rack it won't even be possible to use cookie session without secret. :)

Have a good one
