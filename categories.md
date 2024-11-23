---
layout: page
permalink: /categories/
title: Categories
---
<div id="archives">
  <div class="tabs">
    <button class="tab-button active" onclick="switchTab('category')">Categories</button>
    <button class="tab-button" onclick="switchTab('difficulty')">Difficulty</button>
    <button class="tab-button" onclick="switchTab('tag')">Tags</button>
  </div>

  <!-- Categories Tab -->
  <div id="category-tab" class="tab-content active">
    {% for category in site.categories %}
      <div class="archive-group">
        {% capture category_name %}{{ category | first }}{% endcapture %}
        <div id="{{ category_name | slugify }}"></div>
        <h3 class="category-head">
          <a href="#category/{{ category_name | slugify }}">{{ category_name }}</a>
        </h3>

        {% assign posts_in_category = site.categories[category_name] %}
        {% for post in posts_in_category %}
          <article class="archive-item">
            <h5><a href="{{ post.url }}">{{ post.title }}</a></h5>
          </article>
        {% endfor %}
      </div>
    {% endfor %}
  </div>

  <!-- Difficulty Tab -->
  <div id="difficulty-tab" class="tab-content">
    {% assign all_difficulties = site.posts | map: 'difficulty' | uniq %}
    {% for difficulty in all_difficulties %}
      {% if difficulty %}
        <div class="difficulty-group" id="difficulty-{{ difficulty | slugify }}">
          <h4 class="difficulty-head">
            <a href="#difficulty/difficulty-{{ difficulty | slugify }}">{{ difficulty | capitalize }}</a>
          </h4>
          {% assign posts_with_difficulty = site.posts | where: 'difficulty', difficulty %}
          {% for post in posts_with_difficulty %}
            <article class="archive-item">
              <h5><a href="{{ post.url }}">{{ post.title }}</a></h5>
            </article>
          {% endfor %}
        </div>
      {% endif %}
    {% endfor %}
  </div>

  <!-- Tags Tab -->
  <div id="tag-tab" class="tab-content">
    {% assign all_tags = site.posts | map: 'tags' | join: ',' | split: ',' | uniq %}
    {% for tag in all_tags %}
      {% if tag %}
        <div class="tag-group" id="tag-{{ tag | slugify }}">
          <h5 class="tag-head">
            <a href="#tag/tag-{{ tag | slugify }}">{{ tag }}</a>
          </h5>
          {% for post in site.posts %}
            {% if post.tags contains tag %}
              <article class="archive-item">
                <h5><a href="{{ post.url }}">{{ post.title }}</a></h5>
              </article>
            {% endif %}
          {% endfor %}
        </div>
      {% endif %}
    {% endfor %}
  </div>
</div>

<script>
  function switchTab(tabName, targetId = null) {
    const tabs = document.querySelectorAll('.tab-content');
    const buttons = document.querySelectorAll('.tab-button');

    // Activate the relevant tab
    tabs.forEach(tab => {
      tab.classList.remove('active');
      if (tab.id === `${tabName}-tab`) {
        tab.classList.add('active');
      }
    });

    // Update button styles
    buttons.forEach(button => button.classList.remove('active'));
    document.querySelector(`[onclick="switchTab('${tabName}')"]`).classList.add('active');

    // Scroll to the specific section if targetId is provided
    if (targetId) {
      const targetElement = document.getElementById(targetId);
      if (targetElement) {
        targetElement.scrollIntoView({ behavior: 'smooth' });
      }
    }
  }

  // Handle URL hash navigation on page load
  window.addEventListener('load', () => {
    const hash = window.location.hash.substring(1);
    if (hash) {
      const [tabName, sectionId] = hash.split('/');
      if (tabName) {
        switchTab(tabName, sectionId);
      }
    }
  });
</script>

<style>
  .tabs {
    display: flex;
    margin-bottom: 20px;
  }

  .tab-button {
    padding: 10px 20px;
    margin-right: 10px;
    border: 1px solid #ccc;
    background: #f9f9f9;
    cursor: pointer;
  }

  .tab-button.active {
    background: #ddd;
    font-weight: bold;
  }

  .tab-content {
    display: none;
  }

  .tab-content.active {
    display: block;
  }
</style>
