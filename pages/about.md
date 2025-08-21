---
layout: page
title: About
description: IT老兵日记
keywords: Shi Donghua, 石东华
comments: true
menu: 关于
permalink: /about/
---
Java、Python程序员；AI、Web3实践者；关注远程工作和独立开发

## 联系

{% for website in site.data.social %}
* {{ website.sitename }}：[@{{ website.name }}]({{ website.url }})
{% endfor %}

## Skill Keywords

{% for category in site.data.skills %}
### {{ category.name }}
<div class="btn-inline">
{% for keyword in category.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}
