---
---
Welcome.

# Knowledge
[enter...]({{ "/knowledge" | absolute_url }})

# Posts
{% for post in site.categories["blog"] %}
{{ post.date | date_to_string }} [{{ post.title }}]({{ post.url | absolute_url }})
{% endfor %}

# Books
{% for post in site.categories["books"] %}
{{ post.date | date_to_string }} [{{ post.title }}]({{ post.url | absolute_url }})
{% endfor %}
