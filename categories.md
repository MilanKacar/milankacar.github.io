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
    
    <!-- Separate posts with and without difficulty -->
    {% assign posts_with_difficulty = posts_in_category | where: 'difficulty', nil | where_exp: "post", "post.difficulty != nil" %}
    {% assign posts_without_difficulty = posts_in_category | where: 'difficulty', nil %}

    <!-- Group posts by difficulty -->
    {% assign difficulties = "" %}
    {% for post in posts_with_difficulty %}
      {% unless difficulties contains post.difficulty %}
        {% assign difficulties = difficulties | append: post.difficulty | append: "," %}
      {% endunless %}
    {% endfor %}
    {% assign unique_difficulties = difficulties | split: "," | uniq %}

    <!-- Display posts without difficulty first -->
    {% if posts_without_difficulty.size > 0 %}
      <div class="difficulty-group">
        {% for post in posts_without_difficulty %}
          <article class="archive-item">
            <h5><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></h5>
          </article>
        {% endfor %}
      </div>
    {% endif %}

    <!-- Loop through each difficulty level -->
    {% for difficulty in unique_difficulties %}
      <div class="difficulty-group">
        <h4 class="difficulty-head">{{ difficulty | capitalize }}</h4>
        
        <!-- Collect unique tags within this difficulty -->
        {% assign posts_in_difficulty = posts_with_difficulty | where: 'difficulty', difficulty %}
        {% assign tags_in_difficulty = "" %}
        {% for post in posts_in_difficulty %}
          {% for tag in post.tags %}
            {% unless tags_in_difficulty contains tag %}
              {% assign tags_in_difficulty = tags_in_difficulty | append: tag | append: "," %}
            {% endunless %}
          {% endfor %}
        {% endfor %}
        {% assign unique_tags = tags_in_difficulty | split: "," | uniq %}

        <!-- Display posts grouped by tags -->
        {% for tag in unique_tags %}
          {% if tag != "" %}
            <div class="tag-group">
              <h5 class="tag-head">{{ tag }}</h5>
              {% for post in posts_in_difficulty %}
                {% if post.tags contains tag %}
                  <article class="archive-item">
                    <h5><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></h5>
                  </article>
                {% endif %}
              {% endfor %}
            </div>
          {% endif %}
        {% endfor %}
        
        <!-- Display untagged posts within this difficulty -->
        {% for post in posts_in_difficulty %}
          {% if post.tags.size == 0 %}
            <article class="archive-item">
              <h5><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></h5>
            </article>
          {% endif %}
        {% endfor %}
      </div>
    {% endfor %}
  </div>
{% endfor %}
</div>
