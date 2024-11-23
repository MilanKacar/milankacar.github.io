---
layout: page
permalink: /categories/
title: Categories
---
<div id="archives">
  {% for category in site.categories %}
    <div class="archive-group">
      {% capture category_name %}{{ category | first }}{% endcapture %}
      <div id="{{ category_name | slugify }}"></div>
      <h3 class="category-head">
        <a href="#{{ category_name | slugify }}">{{ category_name }}</a>
      </h3>

      {% assign posts_in_category = site.categories[category_name] %}
      
      <!-- Separate posts with and without difficulty -->
      {% assign posts_with_difficulty = posts_in_category | where_exp: "post", "post.difficulty != nil" %}
      {% assign posts_without_difficulty = posts_in_category | where_exp: "post", "post.difficulty == nil" %}

      <!-- Display posts without difficulty first -->
      {% if posts_without_difficulty.size > 0 %}
        <div class="difficulty-group">
          {% for post in posts_without_difficulty %}
            <article class="archive-item">
              <h5><a href="{{ post.url }}">{{ post.title }}</a></h5>
            </article>
          {% endfor %}
        </div>
      {% endif %}

      <!-- Group posts by difficulty -->
      {% assign unique_difficulties = posts_with_difficulty | map: 'difficulty' | uniq %}
      {% for difficulty in unique_difficulties %}
        <div class="difficulty-group" id="{{ category_name | slugify }}-{{ difficulty | slugify }}">
          <h4 class="difficulty-head">
            <a href="#{{ category_name | slugify }}-{{ difficulty | slugify }}">{{ difficulty | capitalize }}</a>
          </h4>
          
          <!-- Collect unique tags within this difficulty -->
          {% assign posts_in_difficulty = posts_with_difficulty | where: 'difficulty', difficulty %}
          {% assign tags_in_difficulty = posts_in_difficulty | map: 'tags' | join: ',' | split: ',' | uniq %}

          <!-- Display posts grouped by tags -->
          {% for tag in tags_in_difficulty %}
            {% if tag != "" %}
              <div class="tag-group" id="{{ category_name | slugify }}-{{ difficulty | slugify }}-{{ tag | slugify }}">
                <h5 class="tag-head">
                  <a href="#{{ category_name | slugify }}-{{ difficulty | slugify }}-{{ tag | slugify }}">{{ tag }}</a>
                </h5>
                {% for post in posts_in_difficulty %}
                  {% if post.tags contains tag %}
                    <article class="archive-item">
                      <h5><a href="{{ post.url }}">{{ post.title }}</a></h5>
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
                <h5><a href="{{ post.url }}">{{ post.title }}</a></h5>
              </article>
            {% endif %}
          {% endfor %}
        </div>
      {% endfor %}
    </div>
  {% endfor %}
</div>

