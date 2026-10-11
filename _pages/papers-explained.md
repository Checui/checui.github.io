---
layout: default
permalink: /blog/papers-explained/
title: papers explained
nav: false
---

<div class="post">

  <div class="header-bar">
    <h1>papers explained</h1>
    <h2>short notes where I unpack papers I have read and found worth understanding deeply</h2>
  </div>

{% assign papers = site.posts | where_exp: "post", "post.categories contains 'papers-explained'" %}

{% if papers.size == 0 %}

  <p>Nothing here yet — check back soon.</p>

{% else %}

  <ul class="post-list">
    {% for post in papers %}
      {% assign read_time = post.content | number_of_words | divided_by: 180 | plus: 1 %}
      {% assign year = post.date | date: "%Y" %}
      <li>
        <h3>
          <a class="post-title" href="{{ post.url | relative_url }}">{{ post.title }}</a>
        </h3>
        <p>{{ post.description }}</p>
        <p class="post-meta">
          {{ read_time }} min read &nbsp; &middot; &nbsp;
          {{ post.date | date: '%B %d, %Y' }}
        </p>
      </li>
    {% endfor %}
  </ul>

{% endif %}

</div>
