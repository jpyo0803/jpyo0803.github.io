---
title: "Modern C++"
layout: archive
author_profile: true
sidebar:
    nav: "sidebar-category"
permalink: /stuff/modern_cpp/
---


{% assign posts = site.categories.modern_cpp %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}