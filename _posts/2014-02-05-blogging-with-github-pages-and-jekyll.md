---
layout: post
title: "Blogging with GitHub pages and Jekyll"
description: "desc asfda sdfds"
category: 
tags: ["tutorial", "jekyll"]
---
{% include JB/setup %}

Okay. This is going to be another article about GitHub pages, Jekyll and how to start blogging using theese cool tools.
There are plenty of manuals about the topic, but since I'm windows user and spent some time trying to set up everything
I'm going to describe the process from the very beginning.

### Creating GitHub pages

First of all you need to create a site using GitHub pages. Go to the [http://github.com](http://github.com) and create new repository named **USERNAME.github.io**

![create repository](/assets/images/blogging_with_gh/pagessetup.png)

Then you can either wait for about a 10 minutes and try to visit [http://USERNAME.github.io](http://USERNAME.github.io)
to verify that pages website exists or continue set up while pages are being created.

And that's it. Now you're ready to publish anywhere you want and it will be displayed at your homepage. But blogging this way is hard.
So the next step you to do is to configure blog engine make things a bit easier.

### Installing Ruby & Jekyll

#### Ruby

Go to [http://rubyinstaller.org/](http://rubyinstaller.org/) and download RubyInstaller 1.9.3 (for now) version. It worked for me. Don't know whether
there is jekyll for 2.* ruby version, and didn't want to bother thus decided to go on 1.9.3.

Do not install Ruby to the `Program Files` folder or any one containing spaces. It's better to use location suggested by default.

#### Jekyll

Jekyll is simple static site generator. It does all the magic. So you can write your bloh post in Markdown and then Jekyll transforms them to the static HTML
To install Jekyll open run `gem install jekyll` command.

You might face problem with `fast-stemmer` during jekyll installation. I've fixed it by installing [Ruby Development Kit](http://rubyinstaller.org/downloads/).
Extract it either to default location or in Ruby folder. Target folder should not contain any whitespaces.

Then open the folder you installed development kit to in console and run following commands `ruby dk.rb init` `ruby dk.rb install`

### Creating Site

The simplest way to do it is to clone [Jekyll bootstrap](https://github.com/plusjade/jekyll-bootstrap) to the future blog folder

    git clone https://github.com/plusjade/jekyll-bootstrap.git USERNAME.github.com
    cd USERNAME.github.com
    git remote set-url origin https://github.com/USERNAME/USERNAME.github.io.git

To publish changes to your GitHub pages simply push them to the repository

    push origin master

Then you can test if everything is installed correctly by running following command `jekyll serve`

And again there might be an error like `mkdir: Invalid argument`. In this case jekyll needs to be reinstalled to the 1.4.2 version

    gem uninstall jekyll
    gem install jekyll --version "=1.4.2"

After that everyhting shoudl be fine and Jekyll shoudl start normally. So run again `jekyll serve` and 
open [http://localhost:4000](http://localhost:4000) to verify that everything is ok.

Now you're done and can create your first blog post.
