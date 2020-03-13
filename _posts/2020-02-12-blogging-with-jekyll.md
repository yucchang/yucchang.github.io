---
layout: post
title:  Blogging with Jekyll on Github Pages
tags:	[programming, jekyll]  
---

This blog is built by Jekyll on Github Pages. What is **Jekyll?** Jekyll is a static website generator, which can transform plain text into static site without a database and a server. Sounds great, right?! Jekyll is written by **Ruby**, so it's literally a Ruby gem, like Rails, Sinatra... and so many other gems. Of course you can find it on [Rubygems].

## Create a new repo on Github
First, you'll need to create a new repository on Github by clicking the "+" beside your avatar, and hit "New Repository". Fillout the repository name by choosing it wisely. Normally we use username.github.io for the repository name.

When I say **Normally**, but I mean **Should**. : )

For example, I use *yucchang.github.io* as my username is yucchang.

Check out [Github Pages]

![Create a repo](/img/repo_jekyll.png){: .center-block :}

## Install Jekyll
Once you've created the repo, you have to install Jekyll. As I mentioned, Jekyll is a gem , an application that includes some plugins (Ruby gems as well).

**Please note: In this step, everything is running on macOS.**

In whatever directory:
1. Check if Ruby is installed, and it returns Ruby's version. <br/>`$ ruby -v`
2. Use gem to install Jekyll. <br/>`gem install bundler jekyll`
3. To see if Jekyll is properly installed. <br/>`jekyll -v`

## Build up your site
Now, you can choose either by building up your site from the very beginning or by simply just apply themes on [Jekyll theme] which in my case.

### If you decide to build your one and only blog:
1. Run `jekyll new my-site`
2. `$ cd my-site`
3. `$ bundle exec jekyll server`
4. Browse to http://localhost:4000. You are able to see the prototype of you site.
5. You can customized your site by specified the options in `_config.yml` under the root directory. Look up for more details on [Jekyll.com]

### Or build fairly nice one by applying themes:
1. Create a directory. <br/> `mikir myblog`
2. Find your favorite theme, and link to their Github repo.
3. Usually, you can find the instruction on `README`, but in my case:
4. Clone or Download the repo. <br/> `$ git clone https://github.com/repo.git`
5. Move all the files to the directory that just created.
6. `$ bundle exec jekyll serve`
7. Browse to http://localhost:4000

Just as simple as above. I said so cause I made it. LOL

Next, I will bring up some issues that I've dealed with.

### Reference:
- [Github Pages]
- [Jekyll.com]


[Rubygems]: https://rubygems.org/search?utf8=✓&query=jekyll
[Github Pages]: https://help.github.com/en/github/working-with-github-pages/getting-started-with-github-pages
[Jekyll theme]: http://jekyllthemes.org
[Jekyll.com]: https://jekyllrb.com
