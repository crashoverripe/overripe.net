Title: Pelican & GitHub Pages via Actions
Date: 2022-11-06 01:58
Category: Code

## The Problem
So recently I've been checking out [Pelican](https://getpelican.com) as an easy way to get a blog up and running without needing anything fancy and in such a way that security concerns are minimized. In the past I've used classic options like Wordpress and Ghost, but even Ghost (which I love) is heavier than I needed, and provides opportunities for foul play because of the admin interface and database. I've also built static websites, but they get cumbersome to update. Pelican provides a really nice middle ground.

For those who haven't looked into it, Pelican is a lovely little python package that will take structured articles (in this case, in markdown format) and a main config file and bake them down to a static site for you to host however you please. They provide options for easy hosting, including GitHub Pages, but I didn't want to need a development environment set up in order to publish changes.

## The Solution
Lucky for me, GitHub now provides a "Deploy to GitHub Pages" action for GitHub Actions. With a few minor tweaks, I was able to add the necessary steps to install python, Pelican, and then produce my static content.

## Steps
* In your respository's settings go to Pages→Build and deployment→Source and select `GitHub Actions`
* Create a github workflow to build and deploy. You can base it on the default "Static" pages workflow, or just steal [mine](https://github.com/crashoverripe/overripe.net/blob/main/.github/workflows/pages.yml).
