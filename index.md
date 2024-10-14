---
title: 온라인 호스팅 지침
permalink: index.html
layout: home
---

# 콘텐츠 디렉터리

필요한 연습 파일은 [여기서 다운로드](https://github.com/MicrosoftLearning/AZ-700-Designing-and-Implementing-Microsoft-Azure-Networking-Solutions/archive/master.zip)할 수 있습니다.

아래에는 각 연습의 하이퍼링크 목록이 나와 있습니다.

## 연습

{% assign Exercise = site.pages | where_exp:"page", "page.url contains '/Instructions/Exercises'" %}
| 모듈 | 연습 |
| --- | --- | 
{% 연습 내 활동용 %}| {{ activity.Exercise.module }} | [{{ activity.Exercise.title }}{% if activity.Exercise.type %} - {{ activity.Exercise.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

## 데모(제작 중)

{% assign demos = site.pages | where_exp:"page", "page.url contains '/Instructions/Demos'" %}
| 모듈 | 데모 |
| --- | --- |
{% for activity in demos  %}| {{ activity.demo.module }} | [{{ activity.demo.title }}{% if activity.demo.type %} - {{ activity.demo.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

