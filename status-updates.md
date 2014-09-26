---
layout: page
permalink: /status-updates/
title: "Status Updates Archive"
description:
modified: 2014-08-12 22:26:24 +0600
---
<ul class="post-list">
{% for status in site.data.statuses reversed %}
<li>
<a class="twitter-icon" href="https://twitter.com/intent/tweet?text=&quot;{{ status.message }}&quot;%20{{ site.url }}{{ page.url }}%20via%20&#64;{{ site.owner.twitter }}" title="Share on Twitter"><i class="fa fa-twitter"></i> </a>{{ status.message }}<span class="entry-date"><time datetime="{{ status.date }}" itemprop="datePublished">{{ status.date | date: "%b %d, %Y" }}</time>
</li>
{% endfor %}
</ul>