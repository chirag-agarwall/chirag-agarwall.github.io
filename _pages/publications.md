---
layout: archive
title: "Publications"
permalink: /publications/
author_profile: true
---
You can also find my articles on <u><a href="https://scholar.google.com/citations?user=AFEjd1QAAAAJ&hl=en">my Google Scholar profile</a>.</u>

## [Improving Adversarial Robustness by Encouraging Discriminative Features](https://ieeexplore.ieee.org/document/8803601)
### arXiv, 2019

{% if author.googlescholar %}
  You can also find my articles on <u><a href="{{author.googlescholar}}">my Google Scholar profile</a>.</u>
{% endif %}

{% include base_path %}

{% for post in site.publications reversed %}
  {% include archive-single.html %}
{% endfor %}
