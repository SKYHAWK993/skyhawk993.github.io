---
title: "How to post on this site"
date: 2026-06-11
description: "The full publishing workflow: one markdown file, one image folder, one git push."
tags: ["meta"]
---

Publishing a post takes three steps and no code.

## 1. Write a markdown file

Create a file in `src/content/blog/`, say `my-post.md`, starting with a small header block:

```markdown
---
title: "My post title"
date: 2026-06-15
description: "One line shown on the home page."
tags: ["pricing", "ml"]
---

Your post starts here. Plain markdown, with $\LaTeX$ if you want it.
```

The filename becomes the URL — `my-post.md` is published at `/blog/my-post/`.

## 2. Add images

Drop image files into `public/images/` and reference them like any markdown image:

```markdown
![A sample figure](/images/sample-chart.svg)
```

Which renders as:

![A sample figure](/images/sample-chart.svg)

PNG, JPG, SVG, GIF, WebP — all fine. For a caption, use a plain HTML figure:

```html
<figure>
  <img src="/images/sample-chart.svg" alt="A sample figure" />
  <figcaption>Figure 1: captions look like this.</figcaption>
</figure>
```

## 3. Push

```bash
git add . && git commit -m "new post" && git push
```

Vercel rebuilds and deploys the site automatically in about a minute. That's the whole workflow.
