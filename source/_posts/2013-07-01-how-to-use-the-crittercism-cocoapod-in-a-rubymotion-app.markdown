---
layout: post
title: "How to use the Crittercism Cocoapod in a RubyMotion app"
date: 2013-07-01 10:24
comments: false
categories: 
---
[Crittercism](http://www.crittercism.com) is a great way of collecting data and
crash reports from your iOS application whilst it's being used in realtime.

The easiest way to get it up and running in your RubyMotion app is to [use the Coacoapod](http://cocoapods.org/?q=crittercism).

If you haven't setup Coacoapods in your app yet then check out my article
where I [install the ViewDeck Cocoapod](http://paulsturgess.co.uk/blog/2013/05/15/using-native-objective-c-cocoapod-libraries-in-rubymotion/).

Update your `Rakefile` with:

    Motion::Project::App.setup do |app|
      # ...

      app.frameworks += [
        'SystemConfiguration'
      ]

      app.pods do
        pod 'CrittercismSDK'
      end
    end

Locate your Crittercism App id under Settings on the Crittercism website. Update
your `app_delegate.rb` with the following just before you call `makeKeyAndVisible`:

    crittercism_app_id = "51d1421f97c8f273e0000007"
    Crittercism.enableWithAppID(crittercism_app_id)

Now when you build your app and errors get raised, you'll start receiving crash
reports.

