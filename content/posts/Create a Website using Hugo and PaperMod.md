---
title: "Create a Website Using Hugo, PaperMod, and QOwnNotes"
slug: "create-website-hugo-papermod-qownnotes"
date: 2025-10-23
draft: false
categories: ["Tech"]
tags:
  - hugo
  - papermod
  - github
  - linux
  - git
  - qownnotes
  - yaml
summary: "Step-by-step guide to building a personal website using Hugo, the PaperMod theme, and QOwnNotes. Covers setup on Fedora Linux, GitHub Pages deployment, YAML configuration, and workflow automation."
description: "A detailed guide to creating and deploying a Hugo website using the PaperMod theme and QOwnNotes on Linux. Includes installation steps, Git and GitHub setup, YAML configuration, and a complete publishing workflow for GitHub Pages."
keywords: ["Hugo", "PaperMod", "QOwnNotes", "GitHub Pages", "Fedora Linux", "YAML", "website creation", "static site generator"]
cover:
  alt: "Step-by-step guide to building a website with Hugo, PaperMod, and QOwnNotes"
---

##  Intro

The following guide details how I originally set up this very website. It also includes sources so you can expand on what I did easier with the official documents from the tools themselves.

My website was set up on [Fedora Linux](https://fedoraproject.org/kde/), however I have placed links throughout to aid others in unique setups.

### Definitions

**[Hugo:](https://gohugo.io/)** A framework used to create websites, commonly used for blogs. 

**[PaperMod:](https://github.com/adityatelange/hugo-PaperMod)** A theme for your Hugo website. Please do consider using [other themes](https://themes.gohugo.io/).

**[Snap:](https://snapcraft.io/)** A package manager commonly used on Linux machines. This makes it easier to maintain an up-to-date version of Hugo.

**[Git:](https://git-scm.com/)** A version control tool you'll be using to maintain the source code and files to your website.

**[GitHub:](https://github.com/)** A platform to host your Git repository; also allows for publishing your website to a domain (for free) at `<username>.github.io`. Here users can create issues and pull requests to make changes to your website, if desired. We'll also be setting up [an action/workflow](#workflow) to automatically publish the website changes whenever we push them to GitHub.

**[QOwnNotes:](https://www.qownnotes.org/)** A markdown editor that displays markdown text in a unique way, still showing the formatting, while also keeping all the plaintext visible. This makes editing really easy since you can see the raw document and the formatted document at the same time.

**[YAML:](https://yaml.org/)** AKA *YAML Ain't Markup Language* is a human-readable language that allows you to configure applications easily. The nested structure makes sense from a readability standpoint, but also makes it easy for computers to understand as well, which is why we'll be using it to configure our blog. Read more about it in the [latest YAML spec](https://yaml.org/spec/1.2.2/).

## Installation and Setup of Git & GitHub


> Before following the guide from The Odin Project, know that they expect that you are using Ubuntu (either the OS itself or within Windows Subsystem for Linux), MacOS, or ChromeOS. If you are using Fedora and related distros, you will begin by running the following command to install Git, otherwise, follow the guide from The Odin Project:
```  
sudo dnf install git 
```

1. [Setting Up Git](https://www.theodinproject.com/lessons/foundations-setting-up-git)
2. [Introduction to Git](https://www.theodinproject.com/lessons/foundations-introduction-to-git)
3. [Git Basics](https://www.theodinproject.com/lessons/foundations-git-basics)

Use the above guides from The Odin Project to learn the basics of Git & GitHub.

Importantly, make sure you use a GitHub private email address, set your default repository to `main`, and generate a key so that you can push to your GitHub repository.

Afterwards, create a GitHub repository and name it `<yourusername>.github.io` if you desire to set up a website using GitHub's free domain.

Then, clone the empty repo to your computer to prepare for the installation of Hugo.

## Installation of Snap, a Linux Package Manager

If you are using Linux, I recommend installing Snap. This avoids any errors with out of date versions of Hugo distributed by package managers on various distros. Otherwise, you can skip this step.

Use the [Official Guide](https://snapcraft.io/docs/installing-snapd) to get started.

When you install Snap, it is also important to ensure that Snap is part of your path. If you want to run Snap programs without problems, the following commands will allow you to simply type the name of the snap program to run it.

First, check to see if Snap is already part of the path:

```
echo $PATH
```

If not, this next command will add all Snap programs to the path:

```
export PATH=$PATH:/snap/bin
```

This is important because if you want to run a command like `hugo server` and you installed the program using Snap, your command line will not recognize the `hugo` command unless you added Snap applications to your path.

## Install Hugo

> If you are not on Linux or simply want to get your information from the source, check the [Official Guide](https://gohugo.io/installation/).

Run the following command to install Hugo from Snap:

```
sudo snap install hugo --channel=extended
```

Now verify that Hugo is the [latest version](https://github.com/gohugoio/hugo/releases):

```
hugo version
```

To update Hugo, [a Snap application](https://snapcraft.io/docs/get-started#p-19156-update-an-installed-snap), you'll run:

```
sudo snap refresh hugo
```

## Creating a Hugo Site

In your local git clone of your domain's empty GitHub repository, run the following command to initiate a Hugo site:

```
hugo new site . --format yaml
```
> The `.` represents your current directory. `..` represents your parent directory.

> Make sure you `--format yaml` so that the config file matches up with what PaperMod expects, at least in its documentation. It is also easier to read than toml and json.

## Add theme (PaperMod)

There are many themes to choose from when it comes to Hugo. Browse the official [themes website](https://themes.gohugo.io/) and pick your favorite one.

Afterwards, follow the guide from the theme to install it. For PaperMod, they provided a few methods on their [installation page](https://github.com/adityatelange/hugo-PaperMod/wiki/Installation).

The recommended method of installation is as a Git Submodule:

```
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
git submodule update --init --recursive # needed when you reclone your repo (submodules may not get cloned automatically)
```

This clones just the latest version of PaperMod into the folders.

The reason why we use submodules is because it keeps the Git repositories separate (separate commit histories) and allows for easy updating of PaperMod. To update PaperMod in the future, run the following command:

```
git submodule update --remote --merge
```

> For those curious, an official explanation of Git submodules can be found under [Chapter 7.11 Git Tools - Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules) in the official Git book.

## Editing hugo.yaml

Now you must configure your .yaml file for your website so that it points to the correct theme. You can find a sample .yaml file on the [PaperMod Installation Wiki](https://github.com/adityatelange/hugo-PaperMod/wiki/Installation#sample-hugoyml). Just know that your website will not build properly with the following discontinued entry:

```
paginate: 5
```

If you are curious about the functionality of any of the .yaml parameters, check them on the [Features](https://github.com/adityatelange/hugo-PaperMod/wiki/Features) & [Variables](https://github.com/adityatelange/hugo-PaperMod/wiki/Variables) pages on the [PaperMod Wiki](https://github.com/adityatelange/hugo-PaperMod/wiki).

## Github Pages

Afterwards, you need to make sure your GitHub repo is set to public so you can publish to GitHub pages, including using the free domain `<username>.github.io`.

Follow the official [Host on GitHub Pages](https://gohugo.io/host-and-deploy/host-on-github-pages) guide from Hugo. To walk through this process.

You'll also want to make use of the [GitHub documentation for pages](https://docs.github.com/en/pages/getting-started-with-github-pages/what-is-github-pages#types-of-github-pages-sites) in order to get the specifics on GitHub set up.

## QOwnNotes

Once the website is set up, I use [QOwnNotes](https://qownnotes.org/) to write the posts in Markdown. I like this tool because the interface can be made as simple or complex as you want it to be, it's loaded with features, and the way it displays Markdown is really nice.

To install from the Snap store:

```
snap install qownnotes
```

I personally use the Flatpak version since that is what I had on my computer already, however you can install this app however you would like by following the [QOwnNotes Installation Guide](https://qownnotes.org/installation).

It is important to mention that QOwnNotes has a SQL Database it stores in the same folder as your notes, along with a trash can. You can disable the trash can, but not the database. This is important because you will have it synced up with GitHub and you may not want those files to show.

Look into using a `.gitignore` file to avoid this problem.

## YAML Front Matter Explanation

Before we go into the workflow to create a blog post, there are a few things that need to be explained first.

The following is an example of a YAML Header:
```
---
date: 2025.10.14 12:25
draft: true
title: 'My First Blog Post'
tags: [hugo, papermod]
categories: [Tech]
---
```
`date`: This will be the date that shows up for the blog post once you publish it to the website. You can set it here. Also in QOwnNotes, `Ctrl+t` will automatically fill in the current date. You can change the QOwnNotes date format to your favorite here.

`draft`: This lets you set the status of your post, whether it is a draft or not. There are ways to configure Hugo/PaperMod to show draft posts if desired, but by default they are hidden on the website. You'll have to mark this as `true` once the post is completed.

`title`: The title that appears on the blog

`tags`: the tags you want to assign to the post

`categories`: The categories of your blog you want to post to

To continue looking into the page variables contained in the .yaml frontmatter, consult the [PaperMod Wiki: Page Variables](https://github.com/adityatelange/hugo-PaperMod/wiki/Variables#page-variables).

## Workflow

Once you have everything set up and ready to go, there will be a general workflow you can follow to publish articles simply.

1. Use QOwnNotes to write your markdown post. If you do not understand Markdown, it's worth giving the [Markdown Guide](https://www.markdownguide.org/) a read.
2. If you want to see a preview of how your post looks, run `hugo server` in your local Git repository to see the changes live at http://localhost:1313/
3. Once your post is ready, change the YAML front matter from `Draft: True` to `Draft: False`.
4. Now we will leverage the power of Git and GitHub to publish your content. Make sure your site is saved and run the following commands in local git repository:  
```
git add .
git commit -m "<summarize your update here>"
git push origin main
```
5. Through the power of GitHub Actions, you should see the updates go live on your website after a few minutes.