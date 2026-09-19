# abdallahi34.github.io

Personal site: projects and write-ups. Plain static HTML/CSS, no build step, no Jekyll/Ruby dependency — GitHub Pages serves it as-is.

## Publish it

1. Push everything in this folder to the root of the `abdallahi34.github.io` repo (must be **public**).
2. In the repo's **Settings → Pages**, set source to "Deploy from a branch" → `main` → `/ (root)`.
3. Wait a couple of minutes, then it's live at `https://abdallahi34.github.io`.

## Add a new write-up

1. Copy `writeups/_template.html` to `writeups/your-post-slug.html`.
2. Fill in the title, date, tags, and body sections (marked with comments in the template).
3. Add any screenshots to `assets/img/` and reference them with `<img src="../assets/img/yourfile.png">`.
4. Add a matching card in `index.html` under the `<section id="writeups">` block — copy one of the existing `.card` blocks, point its link at `writeups/your-post-slug.html`.
5. Commit and push. No build step, it's live as soon as GitHub Pages picks up the change.

## Add a new project

Same pattern under `<section id="projects">` in `index.html` — copy a `.card` block, point it at the repo URL.

## Customizing the look

All colors and spacing live in `assets/css/style.css` as CSS custom properties at the top of the file (`:root { --blue: ...; --teal: ...; }`). Change those and the whole site follows — no need to hunt through individual pages.

## Before publishing any write-up

- Confirm the machine is retired if it's an HTB box (HTB prohibits public write-ups of active machines).
- Check screenshots for your hostname, local file paths, or anything else you don't want public, and crop/redact before adding them.
