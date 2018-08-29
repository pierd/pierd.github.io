---
---
Welcome to my website. There's not much here yet.

# Posts
{% for post in site.categories["blog"] %}
{{ post.date | date_to_string }} [{{ post.title }}]({{ post.url | absolute_url }})
{% endfor %}
[more...]({{ "/blog" | absolute_url }})

# Books
{% for post in site.categories["books"] %}
{{ post.date | date_to_string }} [{{ post.title }}]({{ post.url | absolute_url }})
{% endfor %}
[more...]({{ "/books" | absolute_url }})
