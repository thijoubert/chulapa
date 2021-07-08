---
layout: landingpage
title: Thoughts on Microsoft 365 Security, Compliance and Identity
subtitle: By Thibault Joubert
# excerpt:
header_type: splash
header_img: "https://images.unsplash.com/photo-1494059980473-813e73ee784b?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1200&q=60"
include_on_search: true
show_sociallinks: true
# project_links:
#    - url: https://github.com/dieghernan/chulapa
#      icon: fab fa-github fa-lg
#      label: Follow
---

## Welcome to my blog! 

## <a href="./blog" class="text-dark">Recent posts</a>

{% include_cached components/indexcards.html cacheddocs=site.posts cachedlimit=3 %}

<a class="btn btn-dark btn-lg my-3 text-primary" href="./blog" role="button">More articles here</a>

## <a href="./events" class="text-dark">Events</a>

Coming soon!

<div class="row g-0 pt-5" id="tags">
<div class="col">
	{%- for tag in grouptag -%}
	<a href="#{{- tag.name | replace: " ", "-" -}}" class="btn btn-primary m-1" role="button" style="font-size: min(1.2rem , max(0.8rem , calc(1rem + 0.025*({{ tag.size }}rem - {{ mid }}rem))));"><i class="fa fa-tag mr-2" aria-hidden="true"></i>{{- tag.name -}}<span class="badge rounded-pill chulapa-pill-bg-primary ml-2">{{tag.size}}</span></a>
	{%- endfor -%}
	</div>
</div>
