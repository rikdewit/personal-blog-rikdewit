---
title: "Creating a blog with hugo and Github Pages"
date: 2022-01-04T13:53:20+01:00
author: "Rik"
draft: true
---

Hugo is a static site generator.
This means it creates static html and css files which you then upload to your server to host them.
It is a back to basics approach in a world where javascript bundle sizes are going through the roof.
- THROUGH THE ROOF GIF

You don't need a database to retrieve posts when you can just use html and links the way they were intended in the 90's.

As you can see, the pages on this blog load almost instantly.

## How does it work?

The workflow of writing new posts is very simple:

1. Create a markdown file in your local posts directory.
2. Push the code to your main branch on Github. 
3. A Github action will run hugo and create the static files.
4. The files are hosted on Github Pages.
---

# Hugo
You can follow more detailed instructions on the [Hugo website.](https://gohugo.io/getting-started/quick-start/)
```
brew install hugo
hugo new site your_blog_name
cd your_blog_name
``` 

## Adding the PaperMod theme

You can choose different themes as well. [themes.gohugo.io](https://themes.gohugo.io/)
``` filename=markdown.md
git init
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/papermod 
```
\
In your config.toml set your theme.
```
theme = "papermod"
```

Every theme as its own custom configurations you can set.

---

## Create a new post
```
hugo new posts/my-first-post.md
```

Create a post using the [markdown syntax.](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)
```
---
title: "Creating a blog with hugo and Github Pages"
date: 2022-01-04T13:53:20+01:00
author: "Rik"
draft: true
---

How to make a blog using Hugo.
```

\
Testing your blog locally:
```
hugo server -D -p 3000
```
Open [http://localhost:3000](http://localhost:3000) on your browser to see the result.

If you're satisfied and want to publish your post, change the draft mode.
```
draft: false
```

---

## Hosting on Github Pages



While you could generate your static files using Hugo and put the contents of ./public on a server.
With a Github Workflow you can automate the deployment.

```
mkdir .github
mkdir .github/workflows
touch .github/workflows/gh-pages.yml
```
\
Template of `gh-pages.yml`

```
name: github pages

on:
  push:
    branches:
      - master  # Set a branch to deploy
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true  # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          # extended: true

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: github.ref == 'refs/heads/master'
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
          cname: <your_blog_domain_name>
```
Make sure your branch is set correctly \
Usually `master` or `main`

Change `CNAME` to your domain name if you don't want to use `<github_username>.github.io` as your url\
Also make a CNAME record in your DNS-settings of your DNS-provider pointing to your github.io url

---

## Publish on Github
Create a new Github repository on [github.com](https://github.com) and add your remote

```
git remote add origin  <REMOTE_URL> 
```

Push the code.

```
git add .
git commit -m "My first blog post"
git branch -M master
git push -u origin master
```

## Change Pages settings

Make sure the build action completed successfuly

Go to `settings/pages` on the your github repo\
Set the `Source` to the gh-pages branch\
Set your custom domain name




