# aghassi.github.io — Claude Code Guide

## Overview
This is a personal blog built with [Jekyll Now](https://github.com/barryclark/jekyll-now), hosted on GitHub Pages at `https://aghassi.github.io`. The site is called **"The Corner"**.

## Key Files & Structure

```
_posts/          # Blog post markdown files (main content area)
_layouts/        # Jekyll HTML layouts (default, page, post)
_includes/       # Reusable HTML partials (analytics, disqus, meta, svg-icons)
_sass/           # Sass partials imported by style.scss
images/          # Post images and assets
_config.yml      # Site configuration (name, URL, social links, plugins)
index.html       # Homepage — iterates site.posts and renders excerpts
style.scss       # Main stylesheet, imports from _sass/
about.md         # About page
404.md           # 404 page
CNAME            # Custom domain (aghassi.github.io)
```

## Writing Blog Posts

Posts live in `_posts/` and follow this naming convention:

```
YYYY-MM-DD-Title-Slug.md
```

Every post requires this front matter:

```yaml
---
layout: post
title: "Your Post Title"
published: true   # set to false to draft without publishing
---
```

- Use `published: false` while drafting; flip to `true` to go live.
- Markdown is GitHub Flavored (GFM) via Kramdown.
- Syntax highlighting uses Rouge with the `highlight` CSS class.
- Images are stored in `/images/` and referenced as `![alt]({{site.baseurl}}/images/filename.png)`.
- Post URLs are generated from the title via `permalink: /:title/`.

## Series Posts
For multi-part series, use a consistent title prefix and part number:

```
YYYY-MM-DD-Series-Name-Part-N.md
```

Example title in front matter: `"Series Name, Part 1: Subtitle"`

## Branch Conventions
New posts are drafted on feature branches:

```
blog/<series-or-post-slug>
```

## Local Development
This is a standard Jekyll site. To run locally:

```bash
bundle exec jekyll serve
```

## Configuration Highlights (`_config.yml`)
- **Social links**: GitHub (`aghassi`), Twitter (`d_aghassi`) — others are blank
- **Comments**: Disqus (not configured)
- **Analytics**: Google Analytics (not configured)
- **Plugins**: `jekyll-sitemap`, `jekyll-feed`
