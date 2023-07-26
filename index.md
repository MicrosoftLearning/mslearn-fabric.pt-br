---
title: Instruções online hospedadas
permalink: index.html
layout: home
---

# Exercícios do Microsoft Fabric

Os exercícios a seguir foram projetados para dar suporte aos módulos no [Microsoft Learn](https://aka.ms/learn-fabric).

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %}
| Módulo | Laboratório |
| --- | --- | 
{% for activity in labs  %}| {{ activity.lab.module }} | [{{ activity.lab.title }}{% if activity.lab.type %} — {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

