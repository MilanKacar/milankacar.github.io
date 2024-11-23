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
    {% assign untagged_posts = "" %}

    <!-- Collect unique tags and separate untagged posts -->
    {% for post in posts_in_category %}
      {% if post.tags.size > 0 %}
        {% for tag in post.tags %}
          {% unless tags_in_category contains tag %}
            {% assign tags_in_category = tags_in_category | append: tag | append: "," %}
          {% endunless %}
        {% endfor %}
      {% else %}
        {% assign untagged_posts = untagged_posts | append: post.id | append: "," %}
      {% endif %}
    {% endfor %}
    
    {% assign unique_tags = tags_in_category | split: "," | uniq %}
    {% assign untagged_posts_array = untagged_posts | split: "," %}

    <!-- Display posts grouped by tags -->
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

    <!-- Display untagged posts separately -->
    {% if untagged_posts_array.size > 0 %}
      <div class="tag-group">
        <h4 class="tag-head">Untagged</h4>
        {% for post in posts_in_category %}
          {% if untagged_posts_array contains post.id %}
            <article class="archive-item">
              <h5><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></h5>
            </article>
          {% endif %}
        {% endfor %}
      </div>
    {% endif %}
  </div>
{% endfor %}
</div>
