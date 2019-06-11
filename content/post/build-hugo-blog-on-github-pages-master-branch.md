---
title: "Build Hugo Blog on Github Pages Master Branch"
thumbnailImagePosition: "top"
thumbnailImage: https://blog.jscrambler.com/content/images/2018/08/jscrambler-blog-hugo-static-site-generator-720px.jpg
coverImage: https://www.codexguides.com/wp-content/uploads/2019/03/Static-site-generator-hugo.png
metaAlignment: center
coverMeta: out
date: 2019-06-11
categories:
- programming
- miscellaneous
tags:
- hugo
- static website
- notes
---

This short article aims to address issues I encountered during hosting personal static blog 
on Github Page.
<!--more-->

# Problem Description

Github Page provides three different ways to host a static personal website, for free.

- host in `/docs` folder
- host in `gh-pages` branch
- host in `master` branch

However, in the case of personal blogs, i.e. using repo `<github_username>.github.io` 
limits the option to just build the website on `master` branch.

It indeed created some minor issues on publishing flow and setup when I switched from `jekyll` 
to `hugo`, and it is not that straight-forward on the official docs (they are great docs), hence this article.

# Solution

### 1. Install Hugo and necessary notes

[Click to view offical docs.](https://gohugo.io/documentation/)

Docs are pretty good, and IMHO Hugo has the most UI friendly 
and comprehensive docs among its competitors.

[More docs here on hosting.](https://gohugo.io/hosting-and-deployment/hosting-on-github/)

Note in the second last section, build and publish on `master` branch is omitted because

> Steps should be similar to that of the gh-pages branch, 
with the exception that you will create your GitHub 
repository with the public directory as the root. 

Not cool! Especially inconvenient for people like me, who often enjoys blindly following
instructions in a tutorial or documentation.

So I have to read the `gh-pages` section carefully, and made some tweaks.

### 2. Understand the structure

The website is built when we run the command `hugo`  at project root, the project
root initiated from `hugo new site <project_name>`, all the static content are automatically
stored in `public` folder by default.
 
In the official instructions, they `git worktree add` 
the `gh-pages` to make it become a linked worktree to the main git worktree, and then hosted the
content of `public` folder there. This is just like having a mini-repo in a repo (not accurate at all but you get the idea).

In order to have the static content in `public` at the project root on `master` branch, we
first switch to a separate branch, called `builder`. 

Then we build the project by running `hugo`, by adapting the command in the docs, we could make 
the `master` branch become the mini-repo we want, set up stream to `master` as well.

```
git worktree add -B master public origin/master
```

Checkout to local `master` branch by simply `cd public`, and do `git push`, use `--force` if necessary.

Remember to push your changes in `builder` branch to remote as well.

In summary, use `builder` branch to write, build and publish the static content, use `master` branch to host 
on Github Pages

### 3. For people who are lazy 

For those who are lazy to read, or for lazy publication, I have the following deployment script, I copy-paste
the entire thing here because I want to see how it looks like when a large chunk of code
is rendered using this Hugo Theme.

```
#!/bin/sh

if [[ $(git status -s) ]]
then
    echo "The working directory is dirty. Please commit any pending changes."
    exit 1;
fi

echo "Deleting old publication"
rm -rf public
mkdir public
git worktree prune
rm -rf .git/worktrees/public/

echo "Checking out gh-pages branch into public"
git worktree add -B master public origin/master

echo "Removing existing files"
rm -rf public/*

echo "Generating site"
hugo

echo "Copy README.md"
cp README.md public/README.md

echo "Updating master branch"
cd public && git add --all && git commit -m "Publishing to github personal page master branch"

git push

echo "Updating builder branch"
cd ../
git push
```

Create a `deploy.sh` file at project root and copy paste the above code in.

Check the original script in [my repo](https://github.com/Shadowsong27/Shadowsong27.github.io).

Then follow the steps below:

- always work on `builder` branch locally

- preview blogs with `hugo serve`

- commit your changes on local `builder branch`

- run `bash deploy.sh` 

# Side Note

I originally wanted to write a detailed comparison and user experience between `hugo` and `jekyll`, but 
I think similar articles are all over the Internet, so never mind.

In short, `hugo` is more **architecturally-pleasing** to me, 
but requires some __simple__ tweaks. I also learned `git worktree` because of this, it is pretty cool.

`jekyll` is easier to setup, and to publish as well, but it is slow to build, 
it does not really matter to me at this point because I only have one aritcle, but I do not like 
to see a long list of red/green `git status` result (weirdo alert!).



