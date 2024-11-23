---
layout: page
permalink: /categories/
title: Categories
---

<div id="archives">
{% for category in site.categories %}
  <div class="archive-group">
    {% capture category_name %}{{ category | first }}{% endcapture %}
    <div id="{{ category_name | slugize }}"></div>
    <h3 class="category-head">{{ category_name }}</h3>
    <a name="{{ category_name | slugize }}"></a>

    {% assign posts_in_category = site.categories[category_name] %}
    {% assign tags_in_category = "" %}

    <!-- Collect unique tags from posts in this category -->
    {% for post in posts_in_category %}
      {% for tag in post.tags %}
        {% unless tags_in_category contains tag %}
          {% assign tags_in_category = tags_in_category | append: tag | append: "," %}
        {% endunless %}
      {% endfor %}
    {% endfor %}
    {% assign unique_tags = tags_in_category | split: "," | uniq %}

    <!-- Check if there are tags -->
    {% if unique_tags.size > 1 or (unique_tags.size == 1 and unique_tags.first != "") %}
      <!-- Group posts by tag -->
      {% for tag in unique_tags %}
        {% if tag != "" %}
          <div class="tag-group">
            <h4 class="tag-head">{{ tag }}</h4>
            {% for post in posts_in_category %}
              {% if post.tags contains tag %}
                <article class="archive-item">
                  <h5><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></h5>
                </article>
              {% endif %}
            {% endfor %}
          </div>
        {% endif %}
      {% endfor %}
    {% else %}
      <!-- No tags, display posts directly -->
      {% for post in posts_in_category %}
        <article class="archive-item">
          <h5><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></h5>
        </article>
      {% endfor %}
    {% endif %}
  </div>
{% endfor %}
</div>
