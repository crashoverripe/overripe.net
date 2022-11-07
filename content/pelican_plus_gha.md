Title: Pelican & GitHub Pages via Actions
Date: 2022-11-06 01:58
Category: Code

So recently I've been checking out [Pelican](https://getpelican.com) as an easy way to get a blog up and running without needing anything fancy and in such a way that security concerns are minimized. In the past I've used classic options like Wordpress and Ghost, but even Ghost (which I love) is heavier than I needed, and provides opportunities for foul play because of the admin interface and database. I've also built static websites, but they get cumbersome to update. Pelican provides a really nice middle ground.

### The Problem
For those who haven't looked into it, Pelican is a lovely little python package that will take structured articles (in this case, in markdown format) and a main config file and bake them down to a static site for you to host however you please. They provide options for easy hosting, including GitHub Pages, but I didn't want to need a development environment set up in order to publish changes.

### The Solution
Lucky for me, GitHub now provides a "Deploy to GitHub Pages" action for GitHub Actions. With a few minor tweaks, I was able to add the necessary steps to install python, Pelican, and then produce my static content.

The base workflow GitHub gives you is as follows:
```yml
# Simple workflow for deploying static content to GitHub Pages
name: Deploy static content to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches: ["main"]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow one concurrent deployment
concurrency:
  group: "pages"
  cancel-in-progress: true

jobs:
  # Single deploy job since we're just deploying
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      - name: Setup Pages
        uses: actions/configure-pages@v2
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v1
        with:
          # Upload entire repository
          path: '.'
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v1
```

To this, we really only need to add a few things after our checkout step.

First, we can set up Python using a handy action:
```yml
      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.10' 
```

Now that we have python installed, we need to install our requirements (Pelican):
```yml
      - name: Install Python Packages
        run: pip install -r requirements.txt
```

With Pelican installed, generating our static content is as easy as running `pelican`:
```yml
      - name: Generate Static Content
        run: pelican
```

Lastly, we need to modifythe upload artifact step so it includes only the `output` directory:
```yml
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v1
        with:
          path: 'output'
```

So all together, the changes amount to this, and we're good to go!

```diff
32a33,40
>       - name: Setup Python
>         uses: actions/setup-python@v4
>         with:
>           python-version: '3.10' 
>       - name: Install Python Packages
>         run: pip install -r requirements.txt
>       - name: Generate Static Content
>         run: pelican
38,39c46
<           # Upload entire repository
<           path: '.'
---
>           path: 'output'
```

That's it! With a quick commit, this does everything for us. Now if I want to make a quick change or whip up an article, I can do so on GitHub directly or on any device on which I can push to git. Easy!

## TLDR Quick Steps
* In your respository's settings go to Pages→Build and deployment→Source and select `GitHub Actions`
* Create a github workflow to build and deploy. You can base it on the default "Static" pages workflow, or just steal [mine](https://github.com/crashoverripe/overripe.net/blob/main/.github/workflows/pages.yml).
