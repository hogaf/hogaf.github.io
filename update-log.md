---
layout: page
permalink: /update-log/
title: "Update Log"
description: 
modified: 2014-08-12 17:52:42 +0600
tags: []
jquery: true
---
## This page lists the last 30 commits.



<div id="github-commits"></div>

<script src="{{ site.url }}/assets/js/vendor/github.commits.widget.js"></script>

<script>
$(function() {
	$('#github-commits').githubInfoWidget(
		{ user: 'hogaf', repo: 'hogaf.github.io', branch: 'master', last: 30, limitMessageTo: 30 });
});
</script>  