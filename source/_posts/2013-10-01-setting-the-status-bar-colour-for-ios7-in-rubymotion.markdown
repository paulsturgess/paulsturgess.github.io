---
layout: post
title: "Setting the status bar colour for iOS7 in RubyMotion"
date: 2013-10-01 10:44
comments: false
categories:
---

First things first you need to set `UIViewControllerBasedStatusBarAppearance` in
your `info.plist`.

This is done by updating your `Rakefile` like so:

    app.info_plist['UIViewControllerBasedStatusBarAppearance'] = true

Now you need to implement the method `preferredStatusBarStyle` in your subclassed
`UINavigationController`:

    class MyCustomNavigationController < UINavigationController

      def preferredStatusBarStyle
        UIStatusBarStyleLightContent
      end

    end

    class AppDelegate
      def application(application, didFinishLaunchingWithOptions:launchOptions)
        @window = UIWindow.alloc.initWithFrame(UIScreen.mainScreen.bounds)
        @window.makeKeyAndVisible
        controller = YourFirstController.alloc.init
        @window.rootViewController = MyCustomNavigationController.alloc.initWithRootController(controller)
        return true
      end
    end

Note that `UIStatusBarStyleLightContent` will turn your status bar text color to white.