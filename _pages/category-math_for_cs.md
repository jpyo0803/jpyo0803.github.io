---
title: "Math for CS"
layout: archive
author_profile: true
sidebar:
    nav: "sidebar-category"
permalink: /stuff/math_for_cs/
---


{% assign posts = site.categories.math_for_cs %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}