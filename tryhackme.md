---
layout: page
title: tryhackme write-up
---

<section>
  {% if site.categories.tryhackme.size > 0 %}

    {% capture currentyear %}{{ 'now' | date: "%Y" }}{% endcapture %}
    {% capture firstpostyear %}{{ site.categories.tryhackme.first.date | date: '%Y' }}{% endcapture %}
    {% if currentyear == firstpostyear %}
        <h3>This year's posts</h3>
    {% else %}  
        <h3>{{ firstpostyear }}</h3>
    {% endif %}

    {% for post in site.categories.tryhackme %}
      {% unless post.next %}
        <ul>
      {% else %}
        {% capture year %}{{ post.date | date: '%Y' }}{% endcapture %}
        {% capture nyear %}{{ post.next.date | date: '%Y' }}{% endcapture %}
        {% if year != nyear %}
          </ul>
          <h3>{{ post.date | date: '%Y' }}</h3>
          <ul>
        {% endif %}
      {% endunless %}
        <li><time>{{ post.date | date:"%d %b" }} - </time>
          <a href="{{ post.url | prepend: site.baseurl | replace: '//', '/' }}">
            {{ post.title }}
          </a>
        </li>
    {% endfor %}
    </ul>

  {% endif %}
</section>