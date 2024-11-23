---
layout: page
permalink: /categories/
title: Categories
---

<div id="archives">
{% for category in site.categories %}
  <div class="archive-group">
    {% capture category_name %}{{ category | first }}{% endcapture %}
    <div id="#{{ category_name | slugize }}"></div>
    <h3 class="category-head">{{ category_name }}</h3>
    <a name="{{ category_name | slugize }}"></a>

    {% assign posts_in_category = site.categories[category_name] %}
    
    <div class="tag-group">
      <p>Tags:</p>
      {% assign tags_in_category = "" %}
      {% for post in posts_in_category %}
        {% for tag in post.tags %}
          {% unless tags_in_category contains tag %}
            {% assign tags_in_category = tags_in_category | append: tag | append: "," %}
          {% endunless %}
        {% endfor %}
      {% endfor %}
      
      {% assign unique_tags = tags_in_category | split: "," | uniq %}
      {% for tag in unique_tags %}
        {% if tag != "" %}
          <span class="tag-item">{{ tag }}</span>
        {% endif %}
      {% endfor %}
    </div>

    {% for post in posts_in_category %}
    <article class="archive-item">
      <h4><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></h4>
    </article>
    {% endfor %}
  </div>
{% endfor %}
</div>
