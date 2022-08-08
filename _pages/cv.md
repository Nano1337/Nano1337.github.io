---
layout: archive
title: "Curriculum Vitae"
permalink: /cv/
author_profile: true
redirect_from:
  - /resume
---

{% include base_path %}

Undergraduate student seeking machine learning internship.
Research interests are the intersection of machine learning and biomedical engineering.
Experience in deep learning, machine learning, data collection, domain application, and software development.
<b><a href="https://drive.google.com/file/d/1FNiLDuS5AYGpjZpDHahSQJ7h6ru2rEls/view?usp=sharing">Download Resume</a></b>

# Education
{% for position in site.data.cv_education %}
  {% include cv_position.html %}
{% endfor %}


# Professional Experience
{% for position in site.data.cv_positions %}
  {% include cv_position.html %}
{% endfor %}


# Publications
<ul>
{% for post in site.publications reversed %}
  {% assign cv_venues = "conference-paper,thesis,poster" | split: "," %}
  {% if cv_venues contains post.venue-type %}
    {% include archive-single-cv.html %}
  {% endif %}
{% endfor %}
</ul>


# Selected Projects
* [pcavision](https://github.com/bstee615/cfactor): Code repository for the paper "Prostate Lesion Detection and Salient Feature Assessment Using Zone-Based Classifiers" (Python/OpenCV)
* [LeetCode Practice](https://github.com/Nano1337/LeetCodePractice): Record of completed LeetCode questions (Python/C++)
* [PyTorch Practice](https://github.com/Nano1337/PytorchPractice): Learn PyTorch with me! (PyTorch/OpenCV)
* [hotcold optimize](https://github.com/Nano1337/HotColdOptimize): Code repository for my reinforcement learning project at Arion in optimizing cloud storage based on user behavior (PyTorch/stable-baselines3)

# Honors
* Cornelius Vanderbilt Scholar (Full-Tuition), Fall 2021 - Present
* Equitable Excellence $10k Scholarship, Fall 2021 - Present
* National Merit Scholar, Fall 2021 - Present

# Technical Skills
* Programming Languages: Proficient in Python and C++. Knowledge of MATLAB, Java, JavaScript, SQL
* Machine/Deep Learning: PyTorch, OpenCV, Pandas, NumPy, Matplotlib, Optuna
* Web Development: HTML, CSS, Gradio, HuggingFace
* DevOps: Hydra, Git, Linux Ubuntu, Weights & Biases

# Leadership
* Sponsorship Coordinator @ VandyHacks, Feb 2022 - Present
* Viola Section Leader @ Vanderbilt Commodore Orchestra, Sept 2021 - Present
