---
layout: post
title:  "Update Bash Script to inject links for each Jekyll post created"
date: 2022-09-06 22:58:56 +0530
categories: programming
description: >-
  New jekyll-blog-post generation with front-matter, inline-CSS, link-generation using a Bash Script.
tags: [programming, bash, script, automate, jekyll-blog]
---

<style type='text/css'>#markdown-toc::before{content:'Table of Contents';font-weight:700}#markdown-toc{border:3px solid #aaa;padding:1.5em;margin-left:0;display:inline-block}</style>

* TOC
{:toc}

## What ticked me off?

Everything was running smoothly. The blogs, although amateurish, were coming along nicely.

I had changed my daily driver OS to Kubuntu-22.04, and setup the jekyll local development environment successfully too.

The posts were also being created flawlessly. Each commit to the [GitHub repo](https://github.com/biplobmanna/biplobmanna.github.io), triggered the Jekyll deployment, and I did not get any failure till date.

That's a success story, right? Well, yes. But, but, I still had an issue.

The README in the actual repo still displayed the _default_ contents that come when a Jekyll project is initialized. That irked me enough to do something with it.

Let's customize the README in some way which is actually useful, right?

---

## What's the idea?

The README will have two table with links in it.

The first table will contain links of the categories in my blog.

The second table will contain links of all the posts.

Pretty simple really.

---

## Generating Links

Creating the first table of links are easy, since they are pretty much rigid for now. They are directly written in Markdown.

Creating the second table of links are a bit complicated.

Since the number of posts are ever increasing, every time a post is added, the corresponding links needs to be added to the table. Manually adding links every time a post was added was never going to happen.

So, let's generate the links to the posts dynamically, whenever a new post is created. To do this, let's utilize the same [script used to generate the posts](https://biplobmanna.github.io/programming/2022/09/06/bash-script-for-generating-jekyll-markdown-posts.html), and modify it to inject links into `README.md` whenever a post is successfully generated.

### What links are generated?

For each post, there are 3 links generated: _category, post, source_

The generated links are used to add a row to the table of posts with the columns: date, category, post, source.

Example table:

| Date | Category | Post | Source |
|------|----------|------|--------|
| 2022-09-06 | [programming](https://biplobmanna.github.io/programming/) | ["Update Bash Script to inject links for each Jekyll post created"](https://biplobmanna.github.io/programming/2022/09/06/update-bash-script-to-inject-links-for-each-jekyll-post-created.html) | [📄](https://github.com/biplobmanna/biplobmanna.github.io/blob/main/_posts/2022-09-06-update-bash-script-to-inject-links-for-each-jekyll-post-created.md) |

### Generating the category link

The category link has the following structure:

```text
https://blog_url/category/
```

The category name is already available in the bash script in the variable `$category` which will be used to generate the link.

The blog's URL:

```text
https://biplobmanna.github.io
```

**Category link:**

```text
https://biplobmanna.github.io/$category
```

### Generating the post link

The post link has the following structure:

```text
https://blog_url/category/yyyy/mm/dd/post-filname-without-date.html/
```

The category is available in `$category`

The date in `yyyy/mm/dd` format is available in `$date_in_path`

The `post-filename-without-date` is available in `$parsed_lowercase_title`

**Post Link:**

```text
https://biplobmanna.github.io/$category/$date_in_path/$parsed_lowercase_title.html
```

### Generating the source link

The source link has the following structure:

```text
https://github.com/profile/repo/blob/branch/path/filename
```

The `profile` is `biplobmanna`

The `repo` is `biplobmanna.github.io`

The `branch` is `main`

The `path` is `_posts`

The `filename` is stored inside `$filename`

**Source Link:**

```text
https://github.com/biplobmanna/biplobmanna.github.io/blob/main/_posts/$filename
```

---

## Injecting into Markdown

The above generated links are attached to relevant text in markdown format:

```text
[The text to be displayed](LINK)
```

The generated markdown style text is then injected into `README.md` based on certain conditions:

1. `README.md` exists
2. The `-i` or `--ignore_readme_update` optional argument is not passed, which if passed creates a variable `ignore_readme_update=1`

{%raw%}

```bash
# update README.md
update_readme() {
 # if README.md exists, only update then
 if [ -f "./README.md" ]; then
  printf "| $parsed_date | " >> ./README.md
  printf "[$category](https://biplobmanna.github.io/$category) | " >> README.md
  date_in_path=$(date +"%Y/%m/%d")
  printf "[\"$title\"](https://biplobmanna.github.io/$category/$date_in_path/$parsed_lowercase_title.html) | " >> README.md
  printf "[📄](https://github.com/biplobmanna/biplobmanna.github.io/blob/main/_posts/$filename) |" >> README.md
  printf "\n"  >> README.md
 fi
}


# update README.md with details of blog post
# if ignore_readme_update DNE
if [ -z ${ignore_readme_update+x} ]; then
  update_readme
fi
```

{%endraw%}

The above injection will add a row into the table.

![Bash Script to inject Links into README](/assets/gif/jekyll-README-links-generation-bash-script.gif)

[VIEW FULL SCRIPT](https://github.com/biplobmanna/biplobmanna.github.io/blob/main/create_post)

---

## Some anecdotes

### The frustration of manual work

There was many a stumble along the way, with many a broken links. The most hard work was to manually add links for all the posts which were already present.

During the addition of this functionality to the [script](<https://github.com/biplobmanna/biplobmanna.github.io/blob/main/create_post>) there were 10 posts already present.

It was a massive headache to manually add markdown content and links for each of the already existing posts.

I basically had to do the following steps for each of the post:

1. Copy-Paste the _date_
2. Copy-Paste the _category_ into `[category](https://category_link)`
3. Copy-Paste the _category, date, title_ and the _filename_ into `[title](https://blog/category/yyyy/mm/dd/filename.html)`
4. Copy-Paste the _filename_ into `[📄](https://github_repo_path/filename.md)`

Multiply that by 10, and you have frustration on a frying pan.

It took much groaning, hair pulling and dealing with broken links to get the desired result. Phew!

### The broken links funk

As soon as the posts are pushed into the GitHub repo, the site builder runs and generates the static site.

The date in the post links are determined based on the _datetime_ mentioned in the front matter in each of the post.

The site is deployed into a Docker container running Ubuntu.

I think the Ubuntu running inside the Docker container has a different timezone set than mine (IST +5.30)

Why?

Well, here is where it gets funky.

I had initially deployed a different after 12 am, on 07/SEPT/2022, and the post was also created after 12am on 07/SEPT/2022, so the date in the post link should have been `.../2022/09/07/...` but the date in the link was `.../2022/09/06...` instead, which kinda suggests that in the timezone inside the container, the date was still 06/SEPT/2022 after the datetime conversion from my timezone (IST +5.30) into whatever timezone is set inside.

Why is this relevant?

Well, the link to the blog post generated has the date in the URL `.../yyyy/mm/dd...` and that is generated based on the date of creation, which in the above case did not match the actual generated link of the post. So, broken links! YAY!

How did I resolve this?

Well, I first deleted the post from GitHub. Then I changed the time to be somewhat later, say around 7am-ish and pushed back into GitHub.

Is this a bug? Do I report it?

Someday, maybe.

---

#### Check out the entire series of posts related to Blog Setup

* [How did my GitHub Blog begin?](https://biplobmanna.github.io/programming/2022/09/06/how-did-my-github-blog-begin.html)
* [Bash Script for generating Jekyll Markdown posts](https://biplobmanna.github.io/programming/2022/09/06/bash-script-for-generating-jekyll-markdown-posts.html)
* [Update Bash Script to inject links for each Jekyll post created](https://biplobmanna.github.io/programming/2022/09/06/update-bash-script-to-inject-links-for-each-jekyll-post-created.html)
* [Add metadata to my Jekyll Blog](https://biplobmanna.github.io/programming/2022/09/07/add-metadata-to-my-jekyll-blog.html)
* [Theming my Jekyll Github Blog](https://biplobmanna.github.io/programming/2022/09/08/theming-my-jekyll-github-blog.html)
