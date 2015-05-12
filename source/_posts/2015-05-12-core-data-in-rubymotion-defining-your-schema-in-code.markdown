---
layout: post
title: "Core Data in RubyMotion – Defining your schema in code"
date: 2015-05-12 10:55
comments: false
categories: 
---
One of the things I love about RubyMotion is being able to build applications without
having to heavily rely on XCode. I feel far more comfortable in a text editor, where I
can actually see the code that makes it work.

Typically Core Data schemas are defined in XCode, with text fields and drop down options
used to define the attributes, relationships etc.

Fortunately for RubyMotion developers the fantastic [ruby-xcdm](https://github.com/infinitered/ruby-xcdm)
allows us to define our schema in code.

This may seem like more work, but one of benefits to defining the schema in code is
visibility. With the schema checked into version control, it's easy to see how it has
evolved over time. Who made the changes, when they made those changes and hopefully
the commit message might even say why they made those changes.

The [ruby-xcdm Readme](https://github.com/infinitered/ruby-xcdm/blob/master/README.md) does a great
job of explaining how to set it up. However, I would like to talk a bit about versioning.

If you're coming from a Ruby on Rails background (as I was) then CoreData doesn't really 'do'
migrations like ActiveRecord does. I'd recommend reading up on the term 'lightweight migration' in
the Apple docs. Essentially for simple things, like adding or removing attributes, it can do these
automatically and they can be performed using ruby-xcdm.

To make this work, all you need to do is copy your schema and create a new file.
Just make sure you increment the schema number and as long as you have...

```ruby
task :"build:simulator" => :"schema:build"
```

...in your Rakefile, then the schema will be built and CoreData will perform the migration.

Ruby-xcdm can handle typical relationship types like `has_many`, `has_one` and `belongs_to`. It also
can handle entities that inherit from a base entity - just lookup the `abstract: true` and `parent:` options.

## Shameless plug

This article is part of a series of blog posts about how I built [Title Challenge](http://titlechallenge.com) – A football manager game for iOS.

Others i've written include:

* [Using PaintCode to Dynamically Create Images](http://paulsturgess.co.uk/blog/2015/03/03/using-paintcode-to-dynamically-create-images-in-the-ios-football-manager-game-title-challenge/)
* [Debuging layout issues with RevealApp](http://paulsturgess.co.uk/blog/2015/02/13/using-reveal-app-to-build-a-rubymotion-football-manager-game/)
* [How to use Core Data to make your app faster](http://paulsturgess.co.uk/blog/2015/02/17/how-you-can-use-core-data-to-make-your-rubymotion-app-faster/).
* [Tweeting a Screenshot with UIActivityViewController](http://paulsturgess.co.uk/blog/2015/02/05/tweeting-a-screenshot-in-rubymotion-with-uiactivityviewcontroller/)

Please do check out [Title Challenge on the App Store](http://itunes.apple.com/app/id959982756).
