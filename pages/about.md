---
layout: page
title: About
description: 学通信的也想玩全栈？？
keywords: Chamboin Zhang, 达瓦里希
comments: true
menu: 关于
permalink: /about/
---

典型理工男，痴迷新技术、电子产品，~~做梦~~梦想从全栈开发到运维再到自建 IDC ，正宗垃圾佬。  
微信公众号可能会做吧，以后再说吧。

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
