---
layout: post
title: "Using native Objective-C Cocoapod libraries in RubyMotion"
date: 2013-05-15 13:40
comments: false
categories:
---
Whilst RubyMotion is new, and the number of [RubyMotion specific libraries](http://rubymotion-wrappers.com/)
is growing fast, there are already loads of native Objective-C libraries out there.

These instructions show you how to use [Cocoapods](http://cocoapods.org) in
combination with [Bundler](http://gembundler.com).

Cocoapods is a dependency manager, like [RubyGems](http://rubygems.org), but for
Objective-C projects.

In this example, I've chosen to install [ViewDeck](https://github.com/Inferis/ViewDeck). ViewDeck
makes it easy to implement the sliding panel user interface made popular by the
Facebook iOS app.

The [CocoaPods website](http://cocoapods.org) is a great place to search for
available 'Pods'.

## Summary of steps

* Include the motion-cocoapods gem
* Setup Cocoapods
* Add ViewDeck to your application Rakefile

## Bundler

If you haven't got Bundler setup, I've written some
[instructions for using Bundler with RubyMotion](http://paulsturgess.co.uk/blog/2013/05/13/setting-up-bundler-and-rvm-for-a-rubymotion-app/).

## Cocoapods

Add cocoapods to your Gemfile:

    gem 'motion-cocoapods'

Install the gem via bundler:

    $ bundle

Setup Cocoapods via:

    $ pod setup

## Add ViewDeck to your Rakefile

In your Rakefile you need to add the ViewDeck pod and QuartzCore Framework
(required by ViewDeck):

    Motion::Project::App.setup do |app|
      # Frameworks
      app.frameworks += [
        'QuartzCore'
      ]

      # Cocoapods
      app.pods do
        pod 'ViewDeck'
      end
    end

Finally tell `motion-cocoapods` to download your dependencies by running:

    $ rake pod:install

ViewDeck will be automatically built and linked the next time you build your app.

In future you can grab updated versions by running:

    $ rake pod:update

## Using ViewDeck
I've ported the Objective-C example ViewDeck application over to RubyMotion.

[Feel free to check it out](https://github.com/paulsturgess/ViewDeckExample-RubyMotion).
