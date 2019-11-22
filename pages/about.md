---
layout: page
title: About
description: 学通信的也想玩全栈？？
keywords: Chamboin Zhang, 达瓦里希
comments: true
menu: 关于
permalink: /about/
---

典型理工男，痴迷新技术、电子产品，梦想学完全栈~~做梦~~，正宗垃圾佬。

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
