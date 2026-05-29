# Nguyen-Khang Le — personal website

Source for [lekhang4497.github.io](https://lekhang4497.github.io/) — the academic homepage of
**Nguyen-Khang Le**, JSPS Research Fellow and Ph.D. researcher at JAIST (LLMs & NLP).

## Stack

A single static page — no build step, no dependencies.

```
index.html              # all content
assets/css/style.css    # design tokens (light/dark) + layout
assets/js/main.js        # theme toggle, scroll reveal, nav spy, publication filter
assets/img/profile.jpg  # portrait
assets/favicon.svg      # monogram
.nojekyll               # serve files as-is (no Jekyll processing)
```

Design language follows the OpenAI Apps SDK tokens: neutral grays, a single blue accent,
system sans for body, and Newsreader for display type.

## Develop

Open `index.html` directly, or serve locally:

```bash
python3 -m http.server 8000   # then visit http://localhost:8000
```

## Deploy

GitHub Pages builds automatically from the `master` branch root on every push.

## Editing content

- **Publications** live in `index.html` under *Selected Publications* and *All Publications*.
  Add a first-author paper with the `data-first` attribute so the "First author" filter picks it up.
- **News**, **Awards**, and **Education** are plain lists in the same file.
