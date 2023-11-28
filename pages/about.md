---
layout: page
title: About
permalink: /about/
weight: 3
---

# **About Me**

Hi I am **{{ site.author.name }}** :wave:, I am a software engineer.
I like to solve problems and to write in Java. I like to learn new things and share my knowledge.
I am excited about the new interesting projects!

<div class="row">
{% include about/skills.html title="Programming Skills" source=site.data.programming-skills %}
{% include about/skills.html title="Other Skills" source=site.data.other-skills %}
</div>

<div class="row">

{% include about/timeline.html title="Work Experience"%}
</div>
