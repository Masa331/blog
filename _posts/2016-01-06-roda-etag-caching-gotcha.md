---
layout: post
title:  "ETag caching gotcha in Roda + Nginx"
date:   2016-01-06 21:22:49
comments: true
author: Přemysl Donát
---
# ETag caching gotcha in Roda + Nginx

I'v been playing with ETag caching recently on one of my apps and found weird problem - in development pages were properly cashed but in production they weren't:

![Development vs. production caching]({{site.url}}/assets/images/development_vs_production.png)

Inspecting pages with Firefox's page inspector i'v found difference in ETag header values. In production i get weak Etag but in development i get strong one. I digged into Roda caching plugin but i only found that if you don't pass `{ weak: true }` options to roda `#etag` method, the ETag will be strong.

I tried the page in different browsers but it was all the same. The cache just wouldn't work. Then i tried curling the production site. Curl was returning strong ETag - same as in development. But when i copied all request headers from browser to curl, weak ETags appeared. That's interesting! By trying only one header at a time i found the change happens when i use `Accept-Content: gzip`.

![curling development and production]({{site.url}}/assets/images/curl_development_vs_production.png)

So it looks like gzip does it somehow. And gzipping is done in Nginx. I googled a bit and found following:

* [Weak ETags and on-the-fly gzipping](https://forum.nginx.org/read.php?2,240120,240120#msg-240120)
* [Issue 903: Lost ETag when enable gzip.](https://code.google.com/p/phusion-passenger/issues/detail?id=903)
* [Enable eTag in Nginx for files sent over gzip](https://thinkingandcomputing.com/2014/09/27/enable-etag-nginx-resources-sent-gzip/)
* [Nginx changelog](http://nginx.org/en/CHANGES) - look for verison 1.7.3

TLDR: esponse is gzipped it doesn't have to be byte-to-byte comparable after restore. And that's what strong ETag suggests. Weak ETag on other hand suggests that response might not be byte-to-byte comparable but the semantics are same. And Nginx does it's best to support caching by modifiing the strong ETag to be a weak one.

TLDR: Response before gzip and response after restore from gzip doesn't have to be byte-to-byte comparable. And that's what strong ETag suggests. Weak ETag on other hand suggests that response might not be byte-to-byte comparable but the semantics are same.

So Nginx does it's best to support caching by modifiing the strong ETag to weak one.

The problem is that Roda waits for "hello" ETag. But after Nginx's gzip it's "W/hello". So when compared, ETags aren't same and 200 response with full content is returned.

## How to fix it?

Pass `{ weak: true }` options to Roda's `#etag` method. It will generate and then expect weak ETags and will properly return 304 responses. Looks like this in code:

{% highlight ruby %}
  r.get 'homepage' do
    r.etag('hello', weak: true)

    view('homepage.html')
  end
{% endhighlight %}

The page is now cached! Hooray.

I wonder how Rails solve this? Do they generate weak ETags as a default? Also i wonder if Apache does the same with ETags after gzipping.

### One last thing about Google Chrome

Another thing i found out is that **Google Chrome strips HTTP cache headers for sites with unverified SSL certificate**. Which is my site exactly. So that was initially another problem i had to solve to move on. They say it's to prevent MITM attacks but i don't like it.


Have a good one!
