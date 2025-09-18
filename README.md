# gh-deployment-workflow

Link to deployed site on GitHub Pages: (https://spetsura.github.io/gh-deployment-workflow/)

Solution to the "GitHub Pages Deployment" exercise in "DevOps Projects" from roadmap.sh.

Project URL: https://roadmap.sh/projects/github-actions-deployment-workflow

*A tiny, friendly demo that ships a single `index.html` to the internet using GitHub Actions + GitHub Pages.*

> Goal: learn CI/CD by deploying any change to `index.html` straight to a live URL.

## What you’ll build

- A minimal static site (`index.html`) saying **“Hello, GitHub Actions!”**
- An automated workflow that deploys on every change to `index.html`
- A live GitHub Pages URL you can share

## Quick start

1. **Create the repo** (e.g. `gh-deployment-workflow`) and add these two files:
   - `index.html`
   - `.github/workflows/deploy.yml` (workflow below)
2. **Push to `main`.**  
3. Open **Settings → Pages** and make sure **Source = GitHub Actions**.  
4. Grab your site link:
   - `https://<username>.github.io/<repo>/`

### Minimal `index.html`

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <title>Hello there!</title>
</head>
<body>
  <p>Hello, GitHub Actions!</p>
</body>
</html>
```
## Workflow: deploy only when index.html changes
```yaml
# .github/workflows/deploy.yml
name: Deploy to GitHub Pages

on:
  push:
    branches: [ "main" ]
    paths:
      - "index.html"   # trigger only if index.html changed
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: true

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: "."

  deploy:
    runs-on: ubuntu-latest
    needs: build
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```
## How it works (in plain words):

1. You push a change to index.html on main.
2. GitHub Actions packages your repo as a static artifact.
3. Pages serves that artifact at your public URL.
4. Your change is live. No servers, no manual uploads.

## Troubleshooting

No URL after the first run? Open Settings → Pages and confirm Source = GitHub Actions; re-run the workflow if needed.

Workflow didn’t trigger? Make sure you pushed to main and changed index.html (the paths filter is strict).

404 on the URL? Wait a minute and hard-refresh; Pages needs a moment on first publish.

