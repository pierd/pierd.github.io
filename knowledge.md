---
---

{% include_relative _knowledge/README.md %}

---

{% for page in site.knowledge %}
{% assign parts = page.path | split: "/" %}
{% if parts[2] %}
{% if parts[1] != last_dir %}

  - {{ parts[1] }}
{% endif %}
    - [{{ page.title }}]({{ page.url | relative_url }})
{% else %}

  - [{{ page.title }}]({{ page.url | relative_url }})
{% endif %}
{% assign last_dir = parts[1] %}
{% endfor %}
