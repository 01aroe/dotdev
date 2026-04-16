# Adam Roe - Dot Dev

Personal blog built with [Hugo](https://gohugo.io/) and the [PaperMod](https://github.com/adityatelange/hugo-PaperMod) theme. Posts are Markdown files in this repo. Pushing to `main` triggers an automatic rebuild and deploy via [Netlify](https://www.netlify.com/).

🔗 **Live site:** [adamroe.dev](https://adamroe.dev)

---

## Local Development

```bash
# Clone the repo (including the PaperMod submodule)
git clone --recurse-submodules https://github.com/01aroe/dotdev.git
cd dotdev

# Start the dev server (includes drafts)
hugo server -D
```

Open [http://localhost:1313](http://localhost:1313). The server hot-reloads on every file save.

---

## Creating a New Post

```bash
hugo new posts/my-post-title.md
```

This creates `content/posts/my-post-title.md` with default front matter. Open it and edit:

```yaml
---
title: "My Post Title"
date: 2026-04-16
draft: true
tags: ["engineering", "side-projects"]
description: "A short summary shown on the post list and in SEO meta tags."
cover:
  image: "images/cover.jpg"
  alt: "Cover image description"
showToc: true
---

Your Markdown content goes here.
```

Write the post body in standard Markdown below the front matter.

---

## Front Matter Reference

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `title` | string | Auto from filename | Post title |
| `date` | date | Current date | Publish date, used for sorting |
| `draft` | bool | `true` | Set to `false` to publish |
| `tags` | list | `[]` | Tags for the post (auto-generates tag pages) |
| `description` | string | — | Summary shown on list pages and in meta tags |
| `cover.image` | string | — | Path to a cover image |
| `cover.alt` | string | — | Alt text for the cover image |
| `showToc` | bool | `true` (from config) | Show/hide table of contents for this post |
| `weight` | int | — | Manual sort order (lower = first) |
| `summary` | string | — | Custom summary (overrides auto-generated) |
| `author` | string | — | Post author name |
| `canonicalURL` | string | — | Canonical URL if cross-posting |

---

## Publishing a Post

1. Set `draft: false` in the post's front matter
2. Commit and push:

```bash
git add .
git commit -m "Add: My Post Title"
git push
```

Netlify detects the push and rebuilds the site automatically (~30 seconds).

---

### Navigation Menu

The nav links are defined in `hugo.toml` under `[menu]`:

```toml
[menu]
  [[menu.main]]
    identifier = 'posts'
    name = 'Posts'
    url = '/posts/'
    weight = 10
```

- `weight` controls the order (lower = further left)
- Add a new `[[menu.main]]` block to add a nav link
- Remove a block to remove a nav link

---

## Tags

Tags are automatic. Add them to any post's front matter:

```yaml
tags: ["engineering", "rust", "side-projects"]
```

Hugo auto-generates:

- `/tags/` — list of all tags with post counts
- `/tags/engineering/` — all posts tagged "engineering"

No configuration needed. New tags appear as soon as a post uses them. Remove all uses of a tag and its page disappears.

---

## Customizing Appearance

### Colors

Create `assets/css/extended/custom.css` (PaperMod auto-loads any CSS file in this directory):

```css
:root {
  --theme: #ffffff;       /* page background (light mode) */
  --entry: #f5f5f5;       /* post card background */
  --primary: #1a1a1a;     /* main text color */
  --secondary: #555555;   /* dates, descriptions */
  --tertiary: #e0e0e0;    /* borders, dividers */
  --content: #333333;     /* post body text */
  --hljs-bg: #f0f0f0;     /* code block background */
  --code-bg: #f0f0f0;     /* inline code background */
  --border: #cccccc;      /* border color */
}

.dark {
  --theme: #1d1e20;
  --entry: #2e2e33;
  --primary: #e0e0e0;
  --secondary: #aaaaaa;
  --tertiary: #3a3a3a;
  --content: #cccccc;
  --hljs-bg: #2b2b2b;
  --code-bg: #2b2b2b;
  --border: #444444;
}
```

### Fonts

In the same `assets/css/extended/custom.css`:

```css
body {
  font-family: "Inter", "Segoe UI", sans-serif;
}

h1, h2, h3, h4, h5, h6 {
  font-family: "Inter", "Segoe UI", sans-serif;
  font-weight: 700;
}

code, pre {
  font-family: "JetBrains Mono", "Fira Code", monospace;
}
```

To load a Google Font, create `layouts/partials/extend_head.html`:

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
```

PaperMod automatically includes this partial in the `<head>` of every page.

---

## Useful Commands

| Command | Description |
|---------|-------------|
| `hugo server -D` | Start dev server with drafts |
| `hugo server` | Start dev server without drafts |
| `hugo new posts/my-post.md` | Create a new post |
| `hugo --minify` | Production build → `public/` |
| `hugo list drafts` | List all draft posts |
| `hugo list all` | List all content files |
| `git submodule update --remote` | Update PaperMod theme to latest |
