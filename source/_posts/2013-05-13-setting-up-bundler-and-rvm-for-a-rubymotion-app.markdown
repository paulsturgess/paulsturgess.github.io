---
layout: post
title: "Setting up Bundler and RVM for a RubyMotion app"
date: 2013-05-13 10:00
comments: false
categories:
---

Create your rvm `.ruby-version` and `.ruby-gemset` files via:

    $ rvm use 1.9.3@yourgemsetname --create --ruby-version

Add bundler to your `Rakefile`

    require 'bundler'
    Bundler.require

Create your `Gemfile` via:

    $ bundle init

Update your `Gemfile`

    source "https://rubygems.org"
    gem "your-favourite-gem"

Install gems via:

    $ bundle

You're done.