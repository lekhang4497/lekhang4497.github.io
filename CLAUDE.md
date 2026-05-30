# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

This repo is the source of **https://lekhang4497.github.io/** — the academic homepage of Nguyen-Khang Le. It is a single hand-authored static page with **no build step, no framework, and no dependencies**.

## Commands

```bash
# Preview locally (open http://localhost:8000) — use a server, not file://,
# so relative asset paths and the web font resolve the same as in production
python3 -m http.server 8000

# Publish: GitHub Pages serves the master branch root directly.
# Any push to master rebuilds the live site (~1 min). There is no CI.
git add -A && git commit -m "..." && git push origin master

# Check the Pages build after pushing
gh api repos/lekhang4497/lekhang4497.github.io/pages/builds/latest --jq '.status, .error.message'
```

There are no tests, linters, or build tooling. `.nojekyll` tells Pages to serve files verbatim (skip Jekyll).

## Architecture

- **All page content is inline in `index.html`.** There is no CMS, templating, data file, or generation step — publications, news, awards, and education are literal HTML. To change what the page says, edit `index.html`. The other files rarely change.
- `assets/css/style.css` — all styling. **Colors live only as CSS variables** in two blocks at the top: `:root` (light) and `html[data-theme="dark"]` (dark). The palette derives from the OpenAI Apps SDK tokens. Never hardcode a color elsewhere; reference `var(--…)`.
- `assets/js/main.js` — vanilla JS, four independent behaviors: theme toggle, scroll-reveal (`.reveal` → `.in`), nav scroll-spy (highlights the section in view), and the publication filter. Plus it auto-fills the footer year (`[data-year]`).
- **Theme is set before paint** by a small inline script in `<head>` (reads `localStorage.theme`, falls back to OS preference) to avoid a flash of the wrong theme. The toggle button just flips `data-theme` and persists it.
- **The page is one scroll with anchored sections.** Each `<section>`/footer has an `id` (`about`, `news`, `publications`, `all-publications`, `awards`, `contact`) that the nav links and scroll-spy depend on. Renaming an `id` means updating the matching `nav__links` `<a href="#…">`.

### Publications are represented twice
- **Selected Publications** (`#publications`): curated `.pub-card` highlights.
- **All Publications** (`#all-publications`): the complete list, in `.year-group` blocks (reverse-chronological), each containing a `.pub-list`.

A paper featured in both must be edited in both places. The **First author** filter is driven entirely by the `data-first` attribute on `<li>` elements — that attribute is the single source of truth, and the filter also hides any `.year-group` left with no visible items.

## Updating content

### Add a publication (full list)
Add an `<li>` inside the correct year's `.pub-list`. Include `data-first` **only if Nguyen-Khang Le is first author** (this is what the filter keys on). Bold the owner's name as `<b>Nguyen-Khang Le</b>`. Link the title if a paper URL exists.

```html
<li data-first>
  <div class="pub-title"><a href="PAPER_URL" target="_blank" rel="noopener">TITLE</a></div>
  <div class="pub-authors"><b>Nguyen-Khang Le</b>, Co Author, Le-Minh Nguyen</div>
  <div class="pub-venue">Venue Name (ABBR), YEAR</div>
  <div class="pub-tags">
    <span class="badge badge--first">First author</span>
    <span class="badge badge--tier">A*</span>
  </div>
</li>
```

- New year? Add a whole block, keeping years in descending order:
  ```html
  <div class="year-group">
    <div class="year-group__label">2027</div>
    <ul class="pub-list"> … </ul>
  </div>
  ```
- **Update the hardcoded total** in `.pub-count strong` (e.g. `34`). The filtered count updates itself; this initial number does not.

### Feature a paper in Selected Publications
Add a `.pub-card` under `<div class="featured">`:

```html
<article class="pub-card">
  <div class="pub-card__meta">
    <span class="pub-card__venue">ACL 2025</span>
    <span class="badge badge--tier">A*</span>
    <span class="badge badge--first">First author</span>
  </div>
  <h3><a href="PAPER_URL" target="_blank" rel="noopener">TITLE</a></h3>
  <p class="pub-card__authors"><b>Nguyen-Khang Le</b>, …</p>
  <p class="pub-card__desc">One-sentence takeaway.</p>
  <div class="pub-card__links">
    <a class="chip" href="PAPER_URL" target="_blank" rel="noopener">…icon… Paper</a>
  </div>
</article>
```

### Badges
- `badge--first` ("First author") — green; pair it with `data-first` on the list item.
- `badge--tier` — blue; the label is free text (`A*`, `A`, `Q1`, `B`, …).

### Add a news item
Prepend to `<ul class="news">` (newest first):
```html
<li><time>2026</time><span class="news__body">What happened. Bold the <b>key thing</b>.</span></li>
```

### Add an award or education entry
Add an `<li>` to the relevant `.timeline` (Awards & Honors and Education each have one):
```html
<li>
  <div class="t-title">Title</div>
  <div class="t-sub">Granting body · short context</div>
  <div class="t-date">2026</div>
</li>
```

### Edit the bio / research statement
Hero one-liner is `.hero__tagline`; the longer text is the `.prose` paragraphs in `#about`; interests are `.tag` chips below it.

### Replace the profile photo
Overwrite `assets/img/profile.jpg` with a square image (≈400×400 or larger). It is referenced by the hero `<img>`, and by `og:image` / `twitter:image` / JSON-LD — those use the production URL, so no path change is needed.

### Change links, email, or affiliation — keep these IN SYNC
The same set of profile links is duplicated in three places, and identity metadata in more. When changing any, update **all** of:
- Hero links: `.hero .links`
- Footer links: `.footer__bottom .links`, and the contact block (`.footer__grid`, the `mailto:` lead)
- `<script type="application/ld+json">` `sameAs` (and `email`, `affiliation`)
- The `mailto:` address appears in several spots — grep for it.

### Theme and typography
Edit the CSS variables at the top of `style.css`. To drop the Newsreader serif and go all-system, change `--serif` and remove the Google Fonts `<link>` in `index.html`.

## Invariants

- Production URLs are hardcoded in the `<head>` meta, Open Graph, and JSON-LD as `https://lekhang4497.github.io/`. Update them if the domain ever changes.
- Section `id`s ↔ `nav__links` hrefs ↔ scroll-spy must stay aligned.
- `data-first` defines the First-author filter; `.pub-count` total is manual.
