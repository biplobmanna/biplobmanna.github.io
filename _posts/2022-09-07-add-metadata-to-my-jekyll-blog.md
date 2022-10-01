---
layout: post
title:  "Add metadata to my Jekyll Blog"
date: 2022-09-07 07:44:11 +0530
categories: programming
description: >-
  SEO Optimizations for my Jekyll Blog. Adding meta tags. Google Site Verification. Sitemap Generation.
tags: [programming, seo, jekyll-blog, google-search-console, meta-tag, optimization]
---

<style type='text/css'>#markdown-toc::before{content:'Table of Contents';font-weight:700}#markdown-toc{border:3px solid #aaa;padding:1.5em;margin-left:0;display:inline-block}</style>

* TOC
{:toc}

## Add SEO \<meta\> tags

Now that the blog was setup and out in the world, and there was a momentum built with a few posts, it was time to do some SEO work. SEO had a wide scope and not something I would tackle in it's entirety for just a small blog, but I guess doing atleast the bare minimum would be good.

### Add \<meta\> tags in \<head\>

To achieve my goals, I used a Jekyll Plugin called [`jekyll-seo-tag`](https://github.com/jekyll/jekyll-seo-tag). <br>
What this plugin does is simple: inject meta tags into `<head>` with data entered in `_config.yml` and/or the page front-matter.

### Add data to be used by jekyll-seo-tag plugin

For the plugin to inject meta tags with relevant data, the data needs to be added to `_config.yml` and/or page's front matter. <br>
Some of the data to be added are listed below.

#### Adding in _config.yml

![Data added to _config.yml](/assets/img/07-09-2022-config_yml.png)

Blog description, author, tagline, and social links which are generally global across all my blog posts are added here. Some of the data, like description, can be also be used as a fallback if corresponding data in not present in the page's front matter.

#### Adding in page's front-matter

![Data added to front-matter of a page](/assets/img/07-09-2022-frontmatter.png)

Description, and tags which are relevant on a per-page basis are added here. <br>
While injecting front matter using the [automated bash script,](https://biplobmanna.github.io/programming/2022/09/06/bash-script-for-generating-jekyll-markdown-posts.html) _description,_ and _tags_ are also injected with some default data, which is to be overridden while writing the blog post.

#### Adding social links

![Social Links to be added in meta tags](/assets/img/07-09-2022-seo-social-links.png)

Also, adding social links to be included in the meta tags as well. These include links to all my public social profiles like twitter, facebook, github, linkedin, and stack-overflow.

 For more details, the [official configuration documentation can be found here.](https://github.com/jekyll/jekyll-seo-tag/blob/master/docs/advanced-usage.md)

---

## Verification and Indexing in Google Search Console

### Verification using Google Site Verification

Verification of ownership of my site in [Google Search Console](https://search.google.com/search-console) is the first step to request indexing of my site from Google.

[Official Documentation.](https://support.google.com/webmasters/answer/9008080?hl=en)

### Adding a sitemap

We need to submit a `sitemap.xml` for the page to be indexed. The sitemap is generated [Jekyll Sitemap Generator Plugin.](https://github.com/jekyll/jekyll-sitemap)

Once the sitemap is generated, we need to submit the sitemap link in [Google Search Console.](https://search.google.com/search-console/sitemaps)

### Requesting Indexing

Once the above steps are done, we need to request indexing from Google. This does not ensure that the page will get indexed, but if it does, it does not specify when that will happen. It's a waiting game now.

---

With that, the basic setup for SEO is done.

**Check out the entire series of posts related to Blog Setup**

* [How did my GitHub Blog begin?](https://biplobmanna.github.io/programming/2022/09/06/how-did-my-github-blog-begin.html)
* [Bash Script for generating Jekyll Markdown posts](https://biplobmanna.github.io/programming/2022/09/06/bash-script-for-generating-jekyll-markdown-posts.html)
* [Update Bash Script to inject links for each Jekyll post created](https://biplobmanna.github.io/programming/2022/09/06/update-bash-script-to-inject-links-for-each-jekyll-post-created.html)
* [Add metadata to my Jekyll Blog](https://biplobmanna.github.io/programming/2022/09/07/add-metadata-to-my-jekyll-blog.html)
* [Theming my Jekyll Github Blog](https://biplobmanna.github.io/programming/2022/09/08/theming-my-jekyll-github-blog.html)
