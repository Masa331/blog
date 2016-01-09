---
layout: post
---

<h2>Blog Posts:</h2>

{% for post in site.posts %}
<div class="post-preview">
  <h3><a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }} - {{ post.date | date: "%e.%-m.%Y" }}</a></h3>
</div>
{% endfor %}
