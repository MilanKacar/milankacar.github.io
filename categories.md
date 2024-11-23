---
layout: page
permalink: /categories/
title: Categories
---

<div id="archives">
  {% for category in site.categories %}
    <div class="archive-group" id="{{ category[0] | slugify }}">
      <h3 class="category-head">{{ category[0] }}</h3>

      {% assign posts_in_category = site.categories[category[0]] %}
      {% assign unique_difficulties = posts_in_category | map: 'difficulty' | uniq | compact %}

      <!-- Display posts without difficulty first -->
      {% assign posts_without_difficulty = posts_in_category | where: 'difficulty', nil %}
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
        <div class="difficulty-group" id="{{ difficulty | slugify }}">
          <h4 class="difficulty-head">
            <a href="{{ site.baseurl }}/categories/{{ category[0] | slugify }}/{{ difficulty | slugify }}">{{ difficulty | capitalize }}</a>
          </h4>

          {% assign posts_in_difficulty = posts_in_category | where: 'difficulty', difficulty %}
          {% assign unique_tags = posts_in_difficulty | map: 'tags' | flatten | uniq | compact %}

          <!-- Display posts grouped by tags -->
          {% for tag in unique_tags %}
            {% if tag != "" %}
              <div class="tag-group" id="{{ tag | slugify }}">
                <!-- Tag heading with link to /categories/{category}/{difficulty}/{tag} -->
                <h5 class="tag-head">
                  <a href="{{ site.baseurl }}/categories/{{ category[0] | slugify }}/{{ difficulty | slugify }}/{{ tag | slugify }}">{{ tag }}</a>
                </h5>
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
