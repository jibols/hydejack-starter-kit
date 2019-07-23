---
layout: about
title: About
#description: 
---
{% assign plugins = site.plugins | default:site.gems %}

<article class="page" role="article">  
  <h2 class="page-title hr">Author</h2>
  {% assign author = site.data.authors[page.author] | default:site.data.authors.first[1] | default:site.author %}

  {% if author.picture %}
    {% include srcset-img.html class="avatar" img=author.picture alt=author.name %}
  {% elsif plugins contains 'jekyll-avatar' %}
    {% assign avatar = author.social.github | default:author.github.username | default:author.github %}
    {% include avatar-tag.html user=avatar %}
  {% endif %}
  
  {{ site.data.authors.author1.about | markdownify }}
<!---  {{ author.about | markdownify }}-->

  <h2 class="page-title hr">Site</h2>
  {{ site.why | markdownify }}

  <h2 class="page-title hr">Resume</h2>
  <!---{{ content }}-->
</article>
