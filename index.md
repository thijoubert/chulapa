---
# layout: landingpage
title: '<span class="chulapa">Chulapa</span>'
subtitle: A full flexible Jekyll theme for Github Pages <br><span id="version"> on development</span>
excerpt: Jekyll template for fast creation of free site on Github Pages. Based on Bootstrap and shipped with lots of features
header_type: splash
header_img: "https://images.unsplash.com/photo-1545290614-5ceedf604139?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1200&q=60"
og_image: "./assets/img/site/banner.png"
include_on_search: true
show_sociallinks: true
project_links:
    - url: https://github.com/dieghernan/chulapa
      icon: fab fa-github fa-lg
      label: Follow
---

## <a href="./blog" class="text-dark">Recent posts</a>
{: .display-3 .mb-5 }

{% include_cached components/indexcards.html cacheddocs=site.posts cachedlimit=3 %}

<div class="text-right mx-3">
		<a href="./blog/" class="btn btn-outline-primary border-0">Blog <i class="fa fa-chevron-right fa-lg" aria-hidden="true"></i><span class="sr-only">Go</span></a>
</div>




        <a class="btn btn-dark btn-lg my-3 text-primary" href="./docs/01-install" role="button">Read the docs</a>
</div>