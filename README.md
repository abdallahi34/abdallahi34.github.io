# abdallahi34.github.io

Personal site and blog — write-ups (HackTheBox / TryHackMe), detection engineering notes, and security projects, including [CloudGuard](https://github.com/abdallahi34/cloudguard).

Built on the [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) Jekyll theme.

## Publishing

This repo builds and deploys itself — no local Ruby/Jekyll install needed.

1. Push this repo's contents to the root of `abdallahi34.github.io` (it must be a **public** repo for free GitHub Pages).
2. In the repo, go to **Settings → Pages → Build and deployment → Source** and select **GitHub Actions**.
3. Push to `main` — the workflow in `.github/workflows/pages-deploy.yml` builds the site and publishes it automatically.
4. The site will be live at `https://abdallahi34.github.io`.

## Writing a new post

Create a file in `_posts/` named `YYYY-MM-DD-title.md` with front matter like:

```yaml
---
title: "Post Title"
author: "abdallahi"
date: YYYY-MM-DD HH:MM:00 +0000
categories: [category]
tags: [tag1, tag2]
render_with_liquid: false
---
```

For a machine write-up, copy `_templates/writeup-template.md` and fill it in — **only publish once the machine is confirmed retired**, per HackTheBox's rules on active machines.

## Local preview (optional)

If you want to preview changes locally before pushing:

```bash
bundle install
bundle exec jekyll serve
```

Then open `http://127.0.0.1:4000`.

## Structure

- `_config.yml` — site settings (title, social links, theme mode)
- `_tabs/` — top-level pages (About, Archives, Categories, Tags)
- `_posts/` — blog posts and write-ups
- `_templates/writeup-template.md` — starting point for a new machine write-up
- `_data/` — author info, contact links, share options
- `assets/img/` — avatar and favicons

## Credits

Theme by [Cotes Chung](https://github.com/cotes2020) — [jekyll-theme-chirpy](https://github.com/cotes2020/jekyll-theme-chirpy), MIT licensed (see `LICENSE`).
