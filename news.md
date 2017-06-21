---
title: News
permalink: "/news/"
layout: page
---

<p class="rss-subscribe">subscribe <a href="{{ "/feed.xml" | prepend: site.baseurl }}">via RSS</a></p>

<ul class="post-list">

{% assign all_news = "" | split: "" %}
{% for bulletin in site.bulletins %}
  {% assign all_news = all_news | push: bulletin %}
{% endfor %}
{% for post in site.posts %}
  {% assign all_news = all_news | push: post %}
{% endfor %}
{% assign sorted_news = all_news | sort:"date" | reverse %}
{% for post in sorted_news %}
  <li>
    <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>

    <h2>
      <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
    </h2>
    <p>
        {{ post.content | strip_html | truncatewords:75}}
    </p>
  </li>
{% endfor %}
</ul>
