---
title: 'Migrating wordpress blog to hugo and hosting it on GitHub Pages'
author: Viktor Halitsyn
date: 2017-11-12T09:59:00-07:00
type: post
thumbnailImage: https://s3-us-west-2.amazonaws.com/vnomad-public/blog_infra/2017/wptohugo/wptohugothumb.png
categories:
  - tech
  - tools
tags:
  - hugo
  - wordpress
  - github
keywords:
  - wordpress
  - hugo
  - github
  - blog

---

Two weeks ago my blog was hosted on a 10$/mo vps instance in [DigitalOcean][1], powered by a couple docker containers, running an ever dynamic [Wordpress][2] platform. Now it's completely static, served from [GitHub Pages][3] servers free. What follows is a small experience reco/guide on how to make this happen.

<!--more-->

TL;DR [link]({{< relref "#migration" >}} ) explaining the process.

# Motivation

{{< image classes="fancybox right" src="https://s3-us-west-2.amazonaws.com/vnomad-public/blog_infra/2017/wptohugo/Wordpress_logo_8.png" thumbnail-width="128px" thumbnail-height="128px">}} 

Running on a small vps with wordpress was nice, but not inspiring: I moved there from (google)blogger platform, which at that time was one of the fastest ways to bootstrap a blog, seeking something, that is more editor/code/content friendly. After all, Wordpress, with the heritage of catering to bloggers around the world, was a promising platform to look at. The things that I was looking for included:

* clean readable design/theme
* easy to use editor, preferably wysiwyg
* easy basics: tags, archive, search, comments, bio etc.

After getting a closer look it turned out that Wordpress is a very bare-bones platform: plugins+themes are the things that make it beautiful and functional. I spent a lot of time looking for a functional simple free themes/plugins that would make my blog look readable on both mobile and desktop. I found one, but ultimately I wasn't happy with it. On top of that my vps docker system kept asking for more RAM to keep MySQL, Varnish and a couple NGinxs satisfied. It was the time I realized I need something way simpler and more developer - oriented.

# Enter HUGO {{< image classes="fancybox right" src="https://s3-us-west-2.amazonaws.com/vnomad-public/blog_infra/2017/wptohugo/hugologo.png" thumbnail-height="64px">}} 

[HUGO][4] is static pages generator, written in Go. It comes with a fast server, that takes about **30** MB mem to run on site of this size(which is not large, but not exactly empty either). You write your posts in markdown, which is currently what I breathe, and you can literally program you pages(with go-like syntax), should you really want to. You need to keep in mind everything is static in the end, thus, should you want your site to be responsive, you'll have to use javascript.

Hugo has _support_ for most things you'd want to have: tags(taxonomies), categories, metadata, templates for different types of content, ready to use themes and internationalization support. That being said **_support_ !== _ready to use out of the box_**. In other words one can build pages that have all the features mentioned, using the framework, but you need to do the legwork... or not ).

## Enter TranquilPeak theme

[TranquilPeak][5] is an [open-source][6] theme that has most of the basics implemented for you, using hugo's framework. You can easily [extend][7] it if you wish. Thus blog runs it, so feel free to look around, but there is a showcase available as well. I won't talk much about it here, as the [docs][8] are quite exhaustive, suffices to say that the theme comes loaded with goodness like

* [disquss][11] for comments
* [fancybox][12] for images
* [google fonts][13] support (essentially any custom css/js support)
* [font-awesome][14] icons
* [highlight.js][15] compatible

<a name="migration"></a>
# Migration experience

{{< image classes="fancybox right" src="https://s3-us-west-2.amazonaws.com/vnomad-public/blog_infra/2017/wptohugo/githubpageslogo.png" thumbnail-width="128px" thumbnail-height="128px">}} 

The goal was to get my wordpress running on GitHub Pages, with a custom domain. I also prefer my content to be versioned separately from the framework, therefore I went with git worktrees workflow as described below.
<br><br>

1. As described [here][9], use the [exporter plugin][10] to get your content out of wordpress. <br>
  I've hit problems right there, on step one:<br>
  **Fatal error: Class 'ZipArchive' not found in /var/www/html/wp-content/plugins/hugo-export.php on line 403**
  <br>
  *Solution*: make sure you have zip installed and enabled(in php.ini) on your machine. php info would be you guiding light. I was running an old `wordpress:4.6.1-fpm` bare container hence `apt-get` was useless. If you are in the same or similar boat that I was - upgrade to latest `wordpress:4.8.3-php7.0-fpm`  then use
  <br>**docker-php-ext-install zip**<br>
  inside the container. Restart the container - now it(export) should work.

2. Install [tranquipeak theme][6]<br>
There were no problems for me doing this. Configuring the theme takes a while, especially if you decide to change fonts, highlights etc.

3. Test everything works by running `hugo server` or `hugo` to generate files.
4. Now host the thing on GitHub Pages, using [this guide][16]<br>
I had several problems here where I could not select the branch to publish from on github, or my CNAME was not working. The remedy was to follow the steps and instructions _*exactly*_ as prescribed by github, namely:

    1. Create a new project.

    2. Create the index.html page

    3. Set your project to be GitHub pages in Settings.

    4. Pull your project from GitHub.

    5. Make sure you have latest git installed and follow the [guide][16]<br>
    I had a problem here where wit git worktree:
    `fatal: Refusing to point HEAD outside of refs/`
    solved, by updating to the latest git.
    
    6 *Add CNAME file, containing your domain name to your gh-pages branch root*

5. **Profit!**

Hope this helps people, should they have trouble. Also, if you have problems following these instructions - let me know in comments.

PS: In my NGinx:Varnish:NGinx:Php-fpm:MySQL Wordpress setup I went overzealous on the HSTS header
```
# Set HSTS to 365 days
add_header Strict-Transport-Security "max-age=31536000; includeSubdomains; preload";
```
So now people, that visited my blog a year ago, and haven't cleared their cache since, will be forced to https, which would yield nothing, since custom domains are not served through https yet from GitHub Pages. Oh well...

[1]: https://www.digitalocean.com
[2]: http://wordpress.com
[3]: https://pages.github.com/
[4]: https://gohugo.io/
[5]: https://themes.gohugo.io/hugo-tranquilpeak-theme/
[6]: https://github.com/kakawait/hugo-tranquilpeak-theme
[7]: https://github.com/kakawait/hugo-tranquilpeak-theme/blob/master/.github/CONTRIBUTING.md
[8]: https://github.com/kakawait/hugo-tranquilpeak-theme/blob/master/docs/user.md
[9]: https://gohugo.io/tools/migrations/
[10]: https://github.com/SchumacherFM/wordpress-to-hugo-exporter
[11]: https://disqus.com/
[12]: http://fancybox.net/
[13]: https://fonts.google.com/
[14]: http://fontawesome.io/
[15]: https://highlightjs.org/
[16]: https://pages.github.com/