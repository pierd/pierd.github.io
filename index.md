---
---
Welcome.

# Knowledge
[enter...]({{ "/knowledge" | relative_url }})

# Posts
{% for post in site.categories["blog"] %}
{{ post.date | date_to_string }} [{{ post.title }}]({{ post.url | relative_url }})
{% endfor %}

# Books
{% for post in site.categories["books"] %}
{{ post.date | date_to_string }} [{{ post.title }}]({{ post.url | relative_url }})
{% endfor %}
