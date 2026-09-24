# Deploying this site

## 1. Where the files go

Unzip this **directly into the root** of your `abdallahi34.github.io` repo ,
`_config.yml`, `_tabs/`, `_posts/`, `.github/`, etc. must sit at the repo's
top level, not inside a subfolder like `mysite/`. If you see
`abdallahi34.github.io/mysite/_config.yml` after committing, that's the bug:
move everything up one level so it's `abdallahi34.github.io/_config.yml`.

## 2. Turn on GitHub Actions as the Pages source (one-time)

Repo → **Settings → Pages → Build and deployment → Source** → select
**GitHub Actions** (not "Deploy from a branch"). This repo builds itself via
`.github/workflows/pages-deploy.yml` , if the source is left on "Deploy from
a branch," your pushes never run that workflow and the site never rebuilds.

## 3. Push to `main`

```bash
git add -A
git commit -m "Update site"
git push origin main
```

The workflow only triggers on pushes to `main` or `master`. Check
**Repo → Actions** after pushing , you should see a new run. Green check =
deployed. Red X = open the run and read the error (most common: a Markdown
front-matter typo).

## 4. Verify

Open `https://abdallahi34.github.io/projects/` , you should see 4 project
cards, each linking to its own page (`/projects/cloudguard/`, etc.). Hard
refresh (Ctrl/Cmd+Shift+R) if it still looks old right after a green run.
