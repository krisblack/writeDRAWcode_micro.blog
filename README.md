# writeDRAWcode — micro.blog theme

Recreates the writedrawcode.com design: device font, warm gray paper (#E7E4E4), black bold headings and links, one 40rem column. Light and dark mode follow the system setting.

## Structure

```
theme.json
layouts/
  index.html            home: intro + avatar, post list, portfolio links
  _default/list.html    archives, categories
  _default/single.html  post or page (posts add author box + prev/next)
  partials/
    head.html  header.html  footer.html
    wordmark.html       logo image (header + footer)
    social-icons.html   RSS, Bluesky, Mastodon, Instagram, Contact (header + footer)
    post-item.html      list entry: titled post with thumbnail, or untitled note
    post-meta.html      date + categories with icons
    author-box.html     bio shown under posts
    pagination.html
static/
  theme.css             all styling, driven by CSS custom properties
```

## Design tokens

Everything lives in `:root` at the top of `static/theme.css`. Dark values are in the `prefers-color-scheme: dark` block right below.

## Micro.blog notes

- Untitled posts render as notes: body plus date, no heading.
- List thumbnails come from the first image in each post.
- Pages with `menu` set (About, Colophon) skip the date, author box and prev/next.
- The avatar comes from `.Site.Params.avatar`; if it's empty, the avatar is left out.
- The footer lede uses the blog's description. Navigation comes from the main menu, with Home and Archive as the fallback.
- Social links and the author bio are written into `social-icons.html` and `author-box.html`.

## Local preview

`hugo server` with this folder as the theme. `.claude/launch.json` points at a throwaway sample site.
