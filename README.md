# writeDRAWcode — micro.blog theme

Recreates the writedrawcode.com design: device font, warm gray paper (#E7E4E4), black bold headings and links, one 40rem column. Light and dark mode follow the system setting.

## Structure

```
theme.json
layouts/
  index.html            home: intro + avatar, post list, latest Now post, portfolio links
  index.archivehtml.html  /archive/ (Blog): titled posts only, no notes
  _default/list.html    archives, categories
  _default/single.html  post or page (posts add author box + prev/next)
  partials/
    head.html  header.html  footer.html
    site-header.html    header markup; takes the nav item to mark as current
    wordmark.html       logo image (header + footer)
    social-icons.html   RSS, Bluesky, Mastodon, Instagram, Contact (header + footer)
    post-item.html      list entry: titled post with thumbnail, or untitled note
    post-meta.html      date + categories with icons
    author-box.html     bio shown under posts
    now-posts.html      returns posts in the "Now" category, newest first
    now-page.html       the /now/ page: big heading, intro panel, all now posts
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
- Now posts are untitled notes in the "Now" category. They show in "What I'm Doing Now" on the home page (latest only) and on /now/ (all), and are left out of the main home list. The "Now" tag itself is hidden in post meta.
- Create a page in micro.blog titled "Now" at /now/ for the Now page. Its body, if any, replaces the default "Short, Quick Daily Notes" panel text.
- Social links and the author bio are written into `social-icons.html` and `author-box.html`.

## Local preview

`hugo server` with this folder as the theme. `.claude/launch.json` points at a throwaway sample site.
