---
layout: home
title: Robot Terror - Projects & Adventures
---

# Robot Terror -- re@liability.engineering
Tech experiments, travel adventures, and code explorations by Robert Taylor.

{% for post in site.posts %}
  <div class="post-entry">
    <h3><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h3>
    <span class="post-date">{{ post.date | date: "%B %d, %Y" }}</span>
    <p>{{ post.excerpt }}</p>
    <a href="{{ post.url | relative_url }}" class="read-more">Read more →</a>
  </div>
  <hr>
{% endfor %}

## About Me

Software engineer, traveler, and tinkerer. Currently exploring the intersection of AI, cloud services, and mobile development. This blog documents my journey building various tech projects while traveling around Taiwan.

## Project Highlights

- **Personal VPN Control System**: A complete AWS-based VPN solution with iOS app control
- **TerrorTunnels**: Secure communication tools for privacy-conscious travelers
- **AI-Generated Development**: Exploring the boundaries of AI-assisted coding

## Connect

- [GitHub](https://github.com/rjamestaylor)
- [BlueSky](https://bsky.app/profile/robotterror.com)
- [LinkedIn](https://linkedin.com/in/rjamestaylor)
