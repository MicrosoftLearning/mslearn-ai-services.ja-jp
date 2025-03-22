---
title: Azure AI サービスの演習
permalink: index.html
layout: home
---

# Azure AI サービスの演習

次の演習は、Microsoft Learn のモジュールをサポートするように設計されています。


{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Exercises'" %} {% for activity in labs  %} {% unless activity.url contains 'ai-foundry' %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endunless %} {% endfor %}
