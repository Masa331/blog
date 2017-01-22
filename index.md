---
layout: post
---

<h2>Blog Posts:</h2>

{% for post in site.posts %}
<div class="post-preview">
{% if post.czech %}
  <h3><a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }} - {{ post.date | date: "%e.%-m.%Y" }}</a> <img src="http://icons.iconarchive.com/icons/icondrawer/flags/16/Czech-Republic-icon.png"> </h3>
{% else %}
  <h3><a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }} - {{ post.date | date: "%e.%-m.%Y" }}</a></h3>
{% endif %}
</div>
{% endfor %}
