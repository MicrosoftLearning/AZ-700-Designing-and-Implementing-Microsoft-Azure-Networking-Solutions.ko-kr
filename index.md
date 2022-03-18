---
title: 온라인 호스팅 지침
permalink: index.html
layout: home
ms.openlocfilehash: 7997ca57a9f8cff5e7f4a03c7de0eaf0c9e436ba
ms.sourcegitcommit: 2ef4734dc1cea1e600b967de23c86716f8fd779e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2022
ms.locfileid: "138095643"
---
# <a name="content-directory"></a>콘텐츠 디렉터리

필요한 연습 파일은 [여기서 다운로드](https://github.com/MicrosoftLearning/AZ-700-Designing-and-Implementing-Microsoft-Azure-Networking-Solutions/archive/master.zip)할 수 있습니다.

아래에 각 연습의 하이퍼링크 목록이 나와 있습니다.

## <a name="exercise"></a>연습

{% assign Exercise = site.pages | where_exp:"page", "page.url contains '/Instructions/Exercises'" %}
| 모듈 | 연습 |
| --- | --- | 
{% for activity in Exercise %}| {{ activity.Exercise.module }} | [{{ activity.Exercise.title }}{% if activity.Exercise.type %} - {{ activity.Exercise.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

