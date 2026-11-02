# writeDRAWcode

Recreates the writedrawcode.com design: device font, warm gray paper (#E7E4E4), black bold headings and links, one 40rem column. Light and dark mode follow the system setting.

This repo works two ways:

- **As a micro.blog theme.** micro.blog reads `layouts/`, `static/` and `theme.json` and ignores everything else.
- **As a self-hosted Hugo site on Netlify.** `hugo.toml`, `content/` and `netlify.toml` turn the same templates into a complete site.

## Structure

```
hugo.toml               site config for self-hosting (micro.blog ignores it)
netlify.toml            Netlify build settings
theme.json              micro.blog theme info
content/
  about.md  colophon.md  now.md
  post/YYYY/MM/DD/*.md  posts and notes
layouts/
  index.html            home: intro + avatar, 5 newest titled posts, latest Now post, portfolio links
  index.archivehtml.html  /archive/ (Blog): all titled posts
  index.feed.xml        /feed.xml, posts only (self-hosted only)
  index.jsonfeed.json   /feed.json, posts only (self-hosted only)
  _default/list.html    categories and other lists
  _default/single.html  post or page (posts add author box + prev/next; /now/ draws the Now page)
  partials/
    head.html  header.html  footer.html
    site-header.html    header markup; takes the nav item to mark as current
    wordmark.html       logo image (header + footer)
    social-icons.html   RSS, Bluesky, Mastodon, Instagram, Contact (header + footer)
    post-item.html      list entry: titled post with thumbnail, or untitled note
    post-meta.html      date + categories with icons
    author-box.html     bio shown under posts
    blog-posts.html     returns titled posts, newest first
    now-posts.html      returns posts in the "Now" category, newest first
    now-page.html       the /now/ page: heading, intro panel, all now posts
    pagination.html
static/
  theme.css             all styling, driven by CSS custom properties
```

## Design tokens

Everything lives in `:root` at the top of `static/theme.css`. Dark values are in the `prefers-color-scheme: dark` block right below.

## Content rules

- Posts have `type` "post" (anything under `content/post/`). Everything else, like About, Colophon and Now, is a page: no date, author box or prev/next.
- Untitled posts render as notes: body plus date, no heading. Keep them under 300 characters, as micro.blog does.
- Now posts are untitled notes in the "Now" category. The latest shows in "What I'm Doing Now" on the home page, and all of them on /now/. The "Now" tag itself is hidden in post meta.
- The home page and Blog page list titled posts only.
- List thumbnails come from the first image in each post.
- The Now page's body, if any, replaces the default "Short, Quick Daily Notes" panel text.
- Social links and the author bio are written into `social-icons.html` and `author-box.html`.

## Writing a post (self-hosted)

Add a Markdown file under `content/post/YYYY/MM/DD/`:

```markdown
---
title: "Blackwing Pencils"
date: 2025-12-02T09:00:00-05:00
categories: ["Productivity"]
---
Post body.
```

Leave out `title` for a note, and use `categories: ["Now"]` for a Now post. The URL becomes `/YYYY/MM/DD/<file name>/`.

## Local preview

```bash
hugo server --buildDrafts
```

Then open http://localhost:1313. The sample posts in `content/post/` are marked `draft: true`, so they show here but never publish. Delete them once real posts are in.

## Deploying to Netlify

1. In Netlify, choose **Add new site → Import an existing project** and pick this GitHub repo.
2. Netlify reads `netlify.toml`, so the build command (`hugo --gc --minify`), publish folder (`public`) and Hugo version are already set.
3. Every push to the production branch redeploys. Pull requests and other branches get preview URLs.
4. When you're ready to switch, add `www.writedrawcode.com` under **Domain management**, then point the domain's DNS (at Cloudflare) at Netlify.

## Still to move off other services

- The avatar is served from Squarespace and the logo images from micro.blog uploads. Put copies in `static/images/` and update `hugo.toml`, `wordmark.html` and `index.html` before closing either account.
- Old Squarespace URLs (`/blog/<slug>`) need redirects to the new ones once posts are imported.
