---
layout: post
title: "Hello Octopress & Github pages"
date: 2013-04-24 19:51
comments: false
categories:
---

[Octopress](http://octopress.org/) is a static blog generator. It uses
[Jekyll](https://github.com/mojombo/jekyll) which was created by Github to run
[Github Pages](http://pages.github.com) and now I'm using it to power my
re-launched blog.

I'm attracted by the prospect of using Octopress because it's dead simple. It
generates the blog content as flat pages so it's super fast. It allows me to
write using [Markdown](http://daringfireball.net/projects/markdown/) and deploy
via [Git](http://git-scm.com/).

There's no server setup and no hosting fees, as it's hosted using Github
Pages. Also it's written in Ruby, so it works with all the tools I'm familiar with.

What follows is more for my reference than anything, so I can remember how I set
things up.

## Initial setup

Create a new Github repo named `yourgithubusername.github.io`.

The name is important here as Github Pages will automatically find this repo and
server up the pages in the `master` branch as your website at
`http://yourgithubusername.github.io`.

Grab Octopress and change directory:

    $ git clone git://github.com/imathis/octopress.git yourgithubusername.github.io
    $ cd yourgithubusername.github.io

Octopress has its' own rvmrc file, but I like to use gemsets, so I created my own
before installing bundler and the required gems:

    $ rvm use 1.9.3@yourgemsetname --create --rvmrc
    $ gem install bundler
    $ bundle install

Install the default theme:

    $ rake install

Octopress has a configuration rake task that automatically sets the repo up for
easy deployment to Github Pages:

    $ rake setup_github_pages

This task does quite a few things. The most important is that it creates a new
`_deploy` directory that is another git repository. This is where Octopress
generates the flat website for deployment to the `master` branch of your repo
on Github.

All the Octopress code used to generate the website into the `_deploy` directory
now lives in new branch called `source`. Note in the `source` branch the
`.gitignore` includes `_deploy` so it doesn't get committed in two places!

This sounds more complicated than it is, Octopress has rake tasks to make this
really easy to manage. It's worth pushing up at this point to check everything
works before tinkering:

    $ rake generate
    $ rake deploy

This copies the generated files into `_deploy`, adds them to git, commits and
pushes them up to the `master` branch.

Visit `http://yourgithubusername.github.io`

Note at this point only the website has been committed, the source needs to be
comitted separately via:

    $ git add .
    $ git commit -m 'Initial source commit'
    $ git push origin source

## Running Octopress locally

Octopress works really well with [POW server](http://pow.cx).

    $ cd ~/.pow
    $ ln -s /path/to/your/octopress/site yoursitename

Now that you’re setup with POW, checkout the `source` branch and run:

    $ rake watch

Load up `http://yoursitename.dev`

## Theme customisation

I'm using the ['Whitespace' theme](https://github.com/lucaslew/whitespace) by lucaslew.
 [Other themes are available](https://github.com/imathis/octopress/wiki/3rd-Party-Octopress-Themes).

I've tweaked the header to remove the search and include navigation links to my Twitter and Github accounts.

I installed the theme by running the following in the root of the `source` branch.
 Say yes when it asks you to overwrite existing files.

    $ git clone git://github.com/lucaslew/whitespace.git .themes/whitespace
    $ rake install['whitespace']
    $ rake generate

## _config.yml

This is where the main site configuration lives. Things like the blog title and various 3rd Party Settings, your Twitter and Github handles etc.

Changes to `_config.yml` will require you to restart the `rake watch` task if
you're POW server.

## Creating this post

As easy as:

    rake new_post["Hello Octopress and Github pages"]

## Deploying to Github Pages

    $ rake generate
    $ rake deploy

Just rememeber to manually commit changes made in the `source` branch.

## Custom domain

Inside your `source` branch run the following:

    echo 'your-domain.com' >> source/CNAME

Deploy up to Gitbub and then point an `A record` (for your naked domain) to `207.97.227.245`

For `www` just point a `CNAME record` to yourgithubusername.github.io

## Working from another machine

As the `_deploy` directory is in the `.gitignore`, the easiest thing is to clone
the `source` branch and then setup the `_deploy` branch manually.

    $ git clone -b source git@github.com:username/username.github.io.git username.github.io
    $ cd username.github.io
    $ bundle install
    $ mkdir _deploy
    $ cd _deploy
    $ git init
    $ git remote add origin git@github.com:username/username.github.io.git
    $ git pull origin master
    $ cd ..
