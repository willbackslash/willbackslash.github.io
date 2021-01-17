---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
---
# My latest post:

<div class="blog-index">  
    {% assign post = site.posts.first %}
    {% assign content = post.content %}
    <h1 class="entry-title">
        {% if page.title %}
        <a href="{{ root_url }}{{ page.url }}">{{ page.title }}</a>
            {% endif %}
            {% if post.title %}
        <a href="{{ root_url }}{{ post.url }}">{{ post.title }}</a>
        {% endif %}
    </h1>
    <div class="entry-content">{{ content }}</div>
</div>