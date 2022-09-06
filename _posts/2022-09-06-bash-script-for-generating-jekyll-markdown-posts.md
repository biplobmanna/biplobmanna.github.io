---
layout: post
title:  "Bash Script for generating Jekyll Markdown posts"
date: 2022-09-06 22:00:18 +0530
categories: programming
---

<style type='text/css'>#markdown-toc::before{content:'Table of Contents';font-weight:700}#markdown-toc{border:3px solid #aaa;padding:1.5em;margin-left:0;display:inline-block}</style>

* TOC
{:toc}

## Did I really get bored again?

It seems like my default pattern of being bored on Sundays was broken. This time I was bored on a Saturday. With nothing much to do, and laziness drowning my desire for physical activity, I started browsing through my ever-increasing list of side projects. However, working on a side project felt like a major chore at that moment, so I thought about writing a couple of blog posts, detailing some of my recent activities.

Energized by the idea, I opened up my local Jekyll environment, and was about to create a new post, when I was struck with the frustration of creating a new file `yyyy-mm-dd-title-of-the-post.md` inside the `_posts` directory.

Not only that, I also had to add front matter to each of the posts, which was different for each post.

{%raw%}

```markdown
---
layout: <layout>
title:  "The Title of the post"
date: yyyy-mm-dd hh:mm:ss +TIMEZONE
categories: <category>
---
```

{%endraw%}

To do this for each and every post is a frustration that cannot be borne. So, in the spirit of a regular developer, let's automate this mundane task.

## How and what to automate?

There are many things to automate here, but for simplicity sake, let's automate the following:

* generation of the post inside `_posts` with the proper _filename_
* injecting front matter with _dynamic_ content

To achieve the above, we must get the dynamic content somehow.

This automation is to be done using a _bash_ script. Since my development environment is Linux (Ubuntu) this will be a good enough method.

The bash script will take in arguments when the script is called form the terminal.

## Parsing arguments in Bash script

For the aforementioned requirements, we need _category, title, layout_ as option-arguments. Since this is a bash script, let's also add _help_ as another category.

Let's define each of those categories a little.

* `title`
  * to be used in front matter, and also used to generate _filename_
  * takes a string as input `eg: "this is a title"`
* `category`
  * to be used in front matter
  * can have values {miscellaneous, programming, poems, songs, stories}
  * has a default value as `miscellaneous`
  * takes a string as input `eg: "programming"`
* `layout` - to be used in front matter
  * to be used in front matter
  * has a default value as `post`
  * takes string as input `eg: "page"`
* `help`
  * has no non-option argument

To achieve the above, we shall use [GNU `getopts`] library, which is installed by default in Linux.

We shall parse both long-option and short-option arguments.

{%raw%}

```bash
SHORT=c:l:t:h
LONG=category:,layout:,title:,help
OPTS=$(getopt --name create_post -o $SHORT --long $LONG -- "$@")
eval set -- "$OPTS"

while :
do
  case "$1" in
    -c | --category )
      category="$2"
      shift 2
      ;;
    -l | --layout )
      layout="$2"
      shift 2
      ;;
    -t | --title )
      title="$2"
      shift 2
      ;;
    -h | --help)
      help
      exit 2
      ;;
    --)
      shift;
      break
      ;;
  esac
done
```

{%endraw%}

Using the `--help` or `-h` option will run the function `help` which in turn calls other functions.

1. `description` - short description of what this bash script does
2. `usage` - syntax for usage
3. `example_usage` - examples of using the script
4. `options` - the different options allowed in the script

Once the arguments are parsed, we also need to check variables & set defaults.

## Parsing non-option arguments

{%raw%}

```bash
# check title cannot be empty
if [[ -z "$title" ]]; then
    echo "--title is non-optional argument"
    echo "Check out some example usages."
    echo
    example_usage
    echo
    ask_help
    echo
    exit 1 # exit with error
fi

# check layout & title
# if empty, populate with default value
[ -z "$category" ] && category=$DEFAULT_CATEGORY
[ -z "$layout" ] && layout=$DEFAULT_LAYOUT
```

{%endraw%}

_Title_ is a non-optional argument, and if it's not provided, show some examples, show instruction to ask help, and exit script.

_Category_, and _Layout_ are set to default values if they are not provided.

## Create variables

Using the values of the options which are set to corresponding variables, let's create more variables which will later be used to generate file, as well as to inject dynamic front matter.

### Parse title

Title is provided as a string `eg: "this is a title"`

This can directly be injected into front matter, but for generating the _filename_ we need to parse it further.

{%raw%}

```bash
echo $title | tr -c '[:alnum:]\~n\r' ' ' | awk '{$1=$1};1' | tr -c '[:alnum:]\n\r' '-' | tr 'A-Z' 'a-z')
```

{%endraw%}

The parsing is done in 4 steps:

1. substitute all special chars (non-alphanum) with space
2. remove start, trailing space + remove multiple-spaces, tabs & replace with single space
3. substitute space with - (hyphen)
4. convert entire string to lowercase

### Parse date, time, timezone

Date, time, and timezone will be used in filename & front matter.

{%raw%}

```bash
parsed_date=$(date +"%Y-%m-%d")
parsed_time=$(date +"%T")
timezone=$(date +"%z")
```

{%endraw%}

### Create filename variable

The filename variable will be used to create the file inside `_posts/` directory.

{%raw%}

```bash
filename="$parsed_date-$parsed_lowercase_title.md"
```

{%endraw%}

## Create Post

Let's create the file in `posts/` directory and inject it with front matter.

### Create file in _posts/

First, let's check if a file with the same `filename` exists. If it does, exist, else, let's create the file.

{%raw%}

```bash
if [[ -f ./_posts/$filename ]]; then
    echo "Error creating file with title='$title'"
    echo "File: $filename already exists inside _posts/"
    echo "Use a different 'title' to create another file."
    echo
    exit 1 #exit with error
else
    # create the file inside _posts
    touch ./_posts/$filename
    echo "created $filename inside _posts/"
    echo
fi
```

{%endraw%}

### Inject front matter into the created file

Each file in _posts expects a front matter:

{%raw%}

```markdown
---
layout: page
title: <category>
permalink: /<category>/
---

```

{%endraw%}

Let's inject the dynamic front-matter from the bash script into the file created above.

{%raw%}

```bash
# inject front matter into the file
echo "---" >> ./_posts/$filename
echo "layout: $layout" >> ./_posts/$filename
echo "title:  \"$title\"" >> ./_posts/$filename
echo "date: $parsed_date $parsed_time $timezone" >> ./_posts/$filename
echo "categories: $category" >> ./_posts/$filename
echo "---" >> ./_posts/$filename
```

{%endraw%}

## Table of Contents

I also want to add a table of contents for each post, which is autogenerated using the headers (h2, h3, h4)

To do this, [multiple options are available](https://blog.webjeda.com/jekyll-toc/), and I chose the default approach in Jekyll provided by Kramdown.

I also want to style the TOC to be a little more appealing, and to achieve that, let's inject some inline CSS as well.

{%raw%}

```bash
printf "\n<style type='text/css'>#markdown-toc::before" >> ./_posts/$filename
printf "{content:'Table of Contents';font-weight:700}" >> ./_posts/$filename
printf "#markdown-toc{border:3px solid #aaa;padding:1.5em;" >> ./_posts/$filename
printf "margin-left:0;display:inline-block}</style>\n" >> ./_posts/$filename
printf "\n* TOC\n" >> ./_posts/$filename
printf "{:toc}\n" >> ./_posts/$filename
```

{%endraw%}

After, everything above has executed perfectly, it will look like:

![Bash Script for generating markdown posts - generated screenshot](/assets/img/06-09-2022-jekyll-blog-post-bash-script-generation.png)
