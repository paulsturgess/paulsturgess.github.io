---
layout: post
title: "How to setup a test only group in Bundler for RubyMotion"
date: 2013-05-16 14:30
comments: false
categories:
---

Coming from a Ruby on Rails background, it's common place to see the Gemfile
declarations in logical groups. This ensures your gems only get required in the
environment they are needed.

A similar setup can be achieved in RubyMotion by tweaking your `Gemfile` and
`Rakefile`.

In your Gemfile:

    source "https://rubygems.org"
    gem 'rake'
    gem 'motion-cocoapods'

    group :spec do
      gem 'motion-redgreen'
    end

In your Rakefile:

    if ARGV.join(' ') =~ /spec/
      Bundler.require :default, :spec
    else
      Bundler.require
    end

Thanks to the [TinyMon](https://github.com/tkadauke/TinyMon) application where
I first saw this setup.