---
title: Azure AI Document Intelligence の演習
permalink: index.html
layout: home
---

# Azure AI Document Intelligence の演習

次の演習は、Microsoft Learn のモジュールをサポートするように設計されています。


{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Exercises'" %} {% for activity in labs  %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endfor %}
