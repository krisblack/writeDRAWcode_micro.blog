# writeDRAWcode

Recreates the writedrawcode.com design: device font, warm gray paper (#E7E4E4), black bold headings and links, one 40rem column. Light and dark mode follow the system setting.

This repo works two ways:

- **As a micro.blog theme.** micro.blog uses `layouts/`, `static/` and `theme.json`. Hugo also merges the params and menus in `hugo.toml` into a site that uses this as a theme, so self-host-only settings (webmention.io, "Created with Hugo") check `on-microblog.html` and switch off on micro.blog.
- **As a self-hosted Hugo site on Netlify.** `hugo.toml`, `content/` and `netlify.toml` turn the same templates into a complete site.

## Structure

```
hugo.toml               site config for self-hosting (self-host-only settings switch off on micro.blog)
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
  _default/single.html  pages; post/single.html  posts (both draw partials/single.html)
  partials/
    head.html  header.html  footer.html
    site-header.html    header markup; takes the nav item to mark as current
    wordmark.html       logo image (header + footer)
    social-icons.html   RSS, Bluesky, Mastodon, Instagram, Contact (header + footer)
    post-item.html      list entry: titled post with thumbnail, or untitled note
    post-meta.html      date + categories with icons
    author-box.html     bio shown under posts
    blog-posts.html     returns titled posts, newest first
    now-posts.html      returns untitled posts (notes), newest first
    now-page.html       the /now/ page: heading, intro panel, all now posts
    single.html         post or page (posts add author box + prev/next; /now/ draws the Now page)
    nav-links.html      main nav links, shared by the desktop nav and the mobile menu
    avatar.html         returns micro.blog's author.avatar, or params.avatar
    on-microblog.html   returns true when building on micro.blog
    webmentions.html    likes, reposts and replies from webmention.io (self-hosted only)
    webmention-author.html  one responder's face and link
    description.html    returns the site blurb (replaces micro.blog's default)
    pagination.html
static/
  theme.css             all styling, driven by CSS custom properties
```

## Design tokens

Everything lives in `:root` at the top of `static/theme.css`. Dark values are in the `prefers-color-scheme: dark` block right below.

## Content rules

- Posts have `type` "post" (anything under `content/post/`). Everything else, like About, Colophon and Now, is a page: no date, author box or prev/next.
- Untitled posts render as notes: body plus date, no heading. Keep them under 300 characters, as micro.blog does.
- Now posts are untitled notes (no category needed). The latest shows in "What I'm Doing Now" on the home page, and all of them on /now/. The "Now" tag itself is hidden in post meta.
- The home page and Blog page list titled posts only.
- List thumbnails come from the first image in each post.
- The Now page's body, if any, replaces the default "Short, Quick Daily Notes" panel text. On micro.blog, create a page titled "Now" at /now/.
- The avatar comes from `params.avatar`, or micro.blog's `params.author.avatar`; if both are empty, the avatar is left out.
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

Leave out `title` for a note; notes show up as Now posts. The URL becomes `/YYYY/MM/DD/<file name>/`.

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

## Webmentions (self-hosted)

On micro.blog, micro.blog receives webmentions and shows replies itself; `head.html` passes through micro.blog's own head links (Webmention, Micropub, IndieAuth, feeds). Self-hosted, the site uses two free services:

- **Receiving: [webmention.io](https://webmention.io).** Every page links to its endpoint (`params.webmention.endpoint`). Builds read its API (`params.webmention.api`) and show likes, reposts and replies under each post. Replies are shown as plain text, never as HTML from other sites. If the API can't be reached, the build warns and carries on.
- **Sending: [webmention.app](https://webmention.app).** After each deploy, it reads the feed and notifies every site the newest post links to.

Setup, once the site is live at www.writedrawcode.com:

1. Sign in at webmention.io with `www.writedrawcode.com`. It signs you in through a `rel="me"` link on the home page to an account such as GitHub, which must link back to the site.
2. In Netlify, under **Build & deploy → Build hooks**, create a hook and copy its URL. In webmention.io's settings, add that URL as the web hook, so each new webmention rebuilds the site and appears.
3. Claim a token at webmention.app/token. In Netlify, under **Build & deploy → Deploy notifications**, add an **Outgoing webhook** for **Deploy succeeded** with the URL `https://webmention.app/check?token=YOUR_TOKEN&limit=1&url=https://www.writedrawcode.com/feed.xml`.

Local previews fetch webmentions at most every 10 minutes (`[caches.getresource]`). Previews on `localhost` never fetch: Hugo blocks any request whose URL mentions localhost.

## Still to move off other services

- The avatar is served from Squarespace and the logo images from micro.blog uploads. Put copies in `static/images/` and update `hugo.toml`, `wordmark.html` and `index.html` before closing either account.
- Old Squarespace URLs (`/blog/<slug>`) need redirects to the new ones once posts are imported.
