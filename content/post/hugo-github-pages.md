+++
date = "2022-12-04"
title = "How to Use Hugo and GitHub Pages to Build Personal Blog"
slug = "how-to-use-hugo-and-github-pages"
categories = [ "Techniques" ]
tags = ["Blog", "Hugo", "GitHub", "Interest"]

+++

# My Needs

I want to build a personal academic blog, which should contain:

- A simple bio page that lists my CV, and publications.
- Pages of my blog articals, like technique articals and reading notes etc.
- **Catagory** and **tag** system to help me organize my published blog articals.

# Hugo + Github Pages = Perfect Solution

## Step1: Install Hugo

Follow the [Tutorial of Installation](https://gohugo.io/installation/) is enough. It provides 4 optional ways to install Hugo on Windows: downloading prebuilt binaries, use Chocolatey package manager, use docker image and run in docker, and build from source. And I choosed to download the prebuilt binary file. 

Note that Go Lang environment is a prerequisition!

## Step2: Configure Github Pages

Follow the [Totorial of Hosting on GitHub Pages](https://gohugo.io/hosting-and-deployment/hosting-on-github/). There are two types of GitHub Pages: **User Pages** and **Project Pages**. They are basically the same, and I choose the former, so I built a repository and named it`JoelYYoung.github.io`. You must replace `JoelYYoung` with your **GitHub account username**.

Github provides two ways to build page from your repository: from branch and from GitHub Action. I choose the former because it seems simpler. And I need to configure in **Settings** of my repository, detailed steps are shown in the following screen shot.

<center>
<img width="700" src="https://github.com/JoelYYoung/JoelYYoung.github.io/raw/master/static/img/set-up-github-pages.png">
<div style="color:black;"> <b> GitHub Pages settings </b>  </div>
</center>


## Step3: Use Hugo to generate a new website

### Create a new website project

Cd to a new directory that you want to maintain the blog. Type `hugo new site blogname` in terminal and execute it to create a new blog website, and Hugo will create a new directory named `blogname` in your working directory. The `blogname` dir defaultly contains the following subdirs: 

<center>
<img width="700" src="https://github.com/JoelYYoung/JoelYYoung.github.io/raw/master/static/img/blogname-dir-content.png">
<div style="color:black;"> <b> sub-directories of blogname </b>  </div>
</center>

### Create a new post artical

Then cd into the `blogname` director and execute `hugo new post/first.md` instruction to create a new `.md`  markdown file named `first.md` under the `./content/post` directory.

### Generate website `HTML` files

Cd into the directory of your **website project** (it should be the `blogname` dir in this example), and then execute `hugo server` to generate the `HTML` files and run a server on `localhost:1313` port. 

Note that Hugo will create a new directory defaultly named `public` to store the website files generated, and you can configure the directory name by editing the `config.toml` file and appending a line `publishDir = "dirname"`.

### Download a theme and apply to website

There are a lot of themes available in [Hugo Theme Store](https://themes.gohugo.io/), you can download one by executing `github clone theme_repo_http_url` command under `blogname/themes` directory. 

Then edit the `config.toml` file and append `theme = "theme-dir-name"` and run `hugo server` again to re-generate the website `HTML`s, and you will find the theme already applied to your website!

## Step 4: Bind with remote GitHub Repository and Push

Execute `git init` command under website project director (`blogname` dir in this example). And use command `git add remote "your repository url"` to bind it to your repository. Then you can push it to `master` branch (it could be other branch but **must** to be consistent with the branch you set in **Step 2**).  Note that before pushing, you should omit `themes` dir by adding it to `.gitignore` file or else errors will happen when GitHub build website from your repository.

## Step 5: Check your outcomes!

Get into **Actions** of your repository, wait until it shows all the steps are finished like shown in the following screen shot, and then you will be able to visit your own page by the address of `https://yougithubusrname.github.io`.

<center>
<img width="700" src="https://github.com/JoelYYoung/JoelYYoung.github.io/raw/master/static/img/states.png">
<div style="color:black;"> <b> check status </b>  </div>
</center>

#### Trouble shooting of Step 5

If you fail to see your website, check Actions of your repository and see the output log to find out reasons.



# Reference

- [blog 1](https://bbs.huaweicloud.com/blogs/306677)
- [blog 2](https://cs.au.dk/~amoeller/spa/1-TIP.pdf)

