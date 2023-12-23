---
layout: default
---

This is a website devoted to sharing designs of amateur radio homebrew equipment. I try to explain 
how the systems work, some of the engineering tradeoffs, and share some testing approaches.

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}"><strong>{{ post.title }}</strong></a>
    </li>
  {% endfor %}
</ul>


Contact: KO4THB@proton.me
