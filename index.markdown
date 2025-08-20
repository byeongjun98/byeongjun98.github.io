---
layout: home
---

<div class="posts">
  {% for post in site.posts %}
    <article class="post">
      <div class="post-header">
        <h2 class="post-title">
          <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
        </h2>
        <time datetime="{{ post.date | date_to_xmlschema }}" class="post-date">{{ post.date | date: "%B %-d, %Y" }}</time>
      </div>
      {{ post.excerpt }}
    </article>
  {% endfor %}
</div>
