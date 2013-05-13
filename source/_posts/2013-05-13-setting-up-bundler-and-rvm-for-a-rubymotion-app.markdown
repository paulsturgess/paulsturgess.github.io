---
layout: post
title: "Setting up Bundler and RVM for a RubyMotion app"
date: 2013-05-13 10:00
comments: false
categories:
---

Create your rvmrc file via:

    $ rvm use 1.9.3@yourgemsetname --create --rvmrc

Add bundler to your `Rakefile`

    require 'bundler'
    Bundler.require

Create your `Gemfile` via:

    $ bundle init

Update your `Gemfile`

    source "https://rubygems.org"
    gem "bubble-wrap"

Install gems

    $ bundle

You're done.