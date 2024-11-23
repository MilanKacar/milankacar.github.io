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
    
    <!-- Collect unique difficulty levels -->
    {% assign difficulty_levels = "easy,medium,hard" | split: "," %}
    {% assign posts_by_difficulty = "" %}
    
    <!-- Loop through difficulty levels -->
    {% for difficulty in difficulty_levels %}
      {% assign posts_with_difficulty = "" %}
      
      <!-- Filter posts with current difficulty -->
      {% for post in posts_in_category %}
        {% if post.difficulty == difficulty %}
          {% assign posts_with_difficulty = posts_with_difficulty | append: post.id | append: "," %}
        {% endif %}
      {% endfor %}
      
      {% assign posts_with_difficulty_array = posts_with_difficulty | split: "," %}
      
      <!-- Display posts if any match current difficulty -->
      {% if posts_with_difficulty_array.size > 1 %}
        <div class="difficulty-group">
          <h4 class="difficulty-head">{{ difficulty | capitalize }}</h4>
          
          <!-- Collect unique tags within this difficulty level -->
          {% assign tags_in_difficulty = "" %}
          {% for post in posts_in_category %}
            {% if posts_with_difficulty_array contains post.id %}
              {% for tag in post.tags %}
                {% unless tags_in_difficulty contains tag %}
                  {% assign tags_in_difficulty = tags_in_difficulty | append: tag | append: "," %}
                {% endunless %}
              {% endfor %}
            {% endif %}
          {% endfor %}
          {% assign unique_tags_in_difficulty = tags_in_difficulty | split: "," | uniq %}
          
          <!-- Display posts grouped by tags within difficulty level -->
          {% for tag in unique_tags_in_difficulty %}
            {% if tag != "" %}
              <div class="tag-group">
                <h5 class="tag-head">{{ tag }}</h5>
                {% for post in posts_in_category %}
                  {% if post.difficulty == difficulty and post.tags contains tag %}
                    <article class="archive-item">
                      <h6><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></h6>
                    </article>
                  {% endif %}
                {% endfor %}
              </div>
            {% endif %}
          {% endfor %}
          
          <!-- Display untagged posts within this difficulty -->
          <div class="tag-group">
            {% for post in posts_in_category %}
              {% if post.difficulty == difficulty and post.tags.size == 0 %}
                <article class="archive-item">
                  <h6><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></h6>
                </article>
              {% endif %}
            {% endfor %}
          </div>
        </div>
      {% endif %}
    {% endfor %}
  </div>
{% endfor %}
</div>
