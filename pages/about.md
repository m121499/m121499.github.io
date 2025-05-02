---
layout: page
title: About
permalink: /about/
weight: 3
---

# **About Me**

Hi I am **{{ site.author.name }}** :wave:,<br>
I am a currently a junior studying in the field of Cybersecurity. I am very intrigued by Offensive Security, Digital Forensics, and Networking. My curiosity for understanding how systems work and how to secure them has driven me to build hands-on projects. I created this website to showcase some projects I have worked on

<div class="row">
{% include about/skills.html title="Skills" source=site.data.programming-skills %}
{% include about/skills.html title="Scripting Languages" source=site.data.other-skills %}
</div>

{% comment %}
<div class="row">
{% include about/timeline.html %}
</div>
{% endcomment %}
