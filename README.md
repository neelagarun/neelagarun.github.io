# neelagarun.github.io

Personal site of Nikhil Neelagaru: posts on machine learning, statistics, physics, and puzzles.
Live at https://neelagarun.github.io.

Built with Jekyll and the [minima](https://github.com/jekyll/minima) theme, hosted on GitHub Pages.

## Running locally

```bash
gem install bundler jekyll jekyll-feed minima
jekyll serve
```

Then open http://localhost:4000.

## Adding a post

Create `_posts/YYYY-MM-DD-title.md` with front matter like:

```yaml
---
layout: post
title: "Post title"
categories: [machine-learning]
---
```

The category decides which section page lists the post: `physics`, `machine-learning`,
`statistics`, `puzzles`, `travel`, or `other`. Math uses MathJax: `\\( ... \\)` inline and
`$$ ... $$` or `\\[ ... \\]` for display.
