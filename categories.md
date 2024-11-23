---
layout: page
permalink: /categories/
title: Categories
---
<link href="https://fonts.googleapis.com/css?family=Work+Sans:400,600,700&display=swap" rel="stylesheet">
<div class="layout">
  <!-- Categories Tab -->
  <input name="nav" type="radio" class="nav category-radio" id="category" checked />
  <div class="page category-page">
    <div class="page-contents">
      <h1>Categories</h1>
      {% for category in site.categories %}
      <div class="archive-group">
        {% capture category_name %}{{ category | first }}{% endcapture %}
        <h3 id="{{ category_name | slugify }}">
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
  </div>
  <label class="nav" for="category">
    <span>
      <svg viewBox="0 0 24 24" width="24" height="24">
        <circle cx="12" cy="12" r="10"></circle>
        <path d="M12 7v10m5-5H7"></path>
      </svg>
      Categories
    </span>
  </label>

  <!-- Difficulty Tab -->
  <input name="nav" type="radio" class="nav difficulty-radio" id="difficulty" />
  <div class="page difficulty-page">
    <div class="page-contents">
      <h1>Difficulties</h1>
      {% assign all_difficulties = site.posts | map: 'difficulty' | uniq %}
      {% for difficulty in all_difficulties %}
      <div class="difficulty-group" id="difficulty-{{ difficulty | slugify }}">
        <h4><a href="#difficulty/difficulty-{{ difficulty | slugify }}">{{ difficulty | capitalize }}</a></h4>
        {% assign posts_with_difficulty = site.posts | where: 'difficulty', difficulty %}
        {% for post in posts_with_difficulty %}
        <article class="archive-item">
          <h5><a href="{{ post.url }}">{{ post.title }}</a></h5>
        </article>
        {% endfor %}
      </div>
      {% endfor %}
    </div>
  </div>
  <label class="nav" for="difficulty">
    <span>
      <svg viewBox="0 0 24 24" width="24" height="24">
        <path d="M12 2v20m10-10H2"></path>
      </svg>
      Difficulties
    </span>
  </label>

  <!-- Tags Tab -->
  <input name="nav" type="radio" class="nav tag-radio" id="tag" />
  <div class="page tag-page">
    <div class="page-contents">
      <h1>Tags</h1>
      {% assign all_tags = site.posts | map: 'tags' | join: ',' | split: ',' | uniq %}
      {% for tag in all_tags %}
      <div class="tag-group" id="tag-{{ tag | slugify }}">
        <h5><a href="#tag/tag-{{ tag | slugify }}">{{ tag }}</a></h5>
        {% for post in site.posts %}
        {% if post.tags contains tag %}
        <article class="archive-item">
          <h5><a href="{{ post.url }}">{{ post.title }}</a></h5>
        </article>
        {% endif %}
        {% endfor %}
      </div>
      {% endfor %}
    </div>
  </div>
  <label class="nav" for="tag">
    <span>
      <svg viewBox="0 0 24 24" width="24" height="24">
        <circle cx="12" cy="12" r="10"></circle>
        <path d="M15 12H9m6-3H9m0 6h6"></path>
      </svg>
      Tags
    </span>
  </label>
</div>

<style>
  * {
    font-family: 'Work Sans', sans-serif;
  }
  .layout {
    display: grid;
    height: 100%;
    width: 100%;
    overflow: hidden;
    grid-template-rows: 50px 1fr;
    grid-template-columns: 1fr 1fr 1fr;
  }

  input[type="radio"] {
    display: none;
  }

  label.nav {
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    border-bottom: 2px solid #8e44ad;
    background: #ecf0f1;
    transition: background 0.4s, color 0.4s;
  }

  input[type="radio"]:checked + .page + label.nav {
    background: #8e44ad;
    color: #ffffff;
  }

  .page {
    grid-column-start: 1;
    grid-row-start: 2;
    grid-column-end: span 3;
    padding: 20px;
    overflow-y: auto;
    display: none;
  }

  input[type="radio"]:checked + .page {
    display: block;
  }

  .archive-group,
  .difficulty-group,
  .tag-group {
    margin-bottom: 20px;
  }

  h1 {
    margin-bottom: 20px;
    font-size: 2rem;
  }

  svg {
    margin-right: 8px;
  }
</style>
