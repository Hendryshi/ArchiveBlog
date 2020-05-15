---
title: Hexo Tutorial
permalink: Hexo-Tutorial
toc: true

thumbnail: /2020/04/04/Hexo-Tutorial/cover.png
categories:
  - Tutorial
tags:
  - Hexo

date: 2020-04-04
update: 2020-05-12
---

Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).
<!-- more -->

## Workflow

### Create a new draft post

``` bash
$ hexo new draft "post-title"
```
That will create a markdown file named by your title, and an image folder which has the same name in sub directory `source\_drafts\`

Note: the space in title should be replaced by "-". As this will be a part of url in your blog

### Write draft blog

Put your categories and tags in your markdown file.
To insert the cover image in your blog, change the image path to `/yyyy/mm/dd/post-title/cover.png` after `permalink`
Write the article after `< !-- more -- >`. 
To insert the image in your blog, put the image file in the image folder

### Run server

``` bash
$ hexo s --draft
```

That will allow you to preview your blog including draft post in local website: http://localhost:4000/

More info: [Server](https://hexo.io/docs/server.html)

### Move draft blog to post folder

``` bash
$ hexo publish "post-title"
```
You can run this command when you finish your draft blog, and you want to publish it.

Note: When the blog is put into the post folder, you change remove the "-" in your title.


### Generate static files

``` bash
$ hexo clean
$ hexo g
```
That will clean and generate your released file in folder `.deploy_git`

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy source code to your Github Repository

``` bash In the Hexo branch
$ git add .
$ git commit -m "comment"
$ git push origin Hexo
```

### Deploy to remote sites 

``` bash
$ hexo d
```
More info: [Deployment](https://hexo.io/docs/one-command-deployment.html)



