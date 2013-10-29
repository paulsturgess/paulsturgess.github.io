---
layout: post
title: "Logging to the console in RubyMotion"
date: 2013-10-29 08:44
comments: false
categories: 
---
The simplest way to output to the console from your RubyMotion app is to use
`puts`. This works fine when your app is running in the simulator. However,
`puts` does not output to the console when your app is running on the device
itself.

You can view the live logs while your app is running on your usb connected device via:

    $ motion device:console

You will see messages from your application alongside those from other apps or
the system itself.

Instead of using `puts`, you'll need to make use of `NSLog`. For example

    NSLog("My debug messge")

Note, however, that the first argument for `NSLog` is actually a [String Format Specifier](https://developer.apple.com/library/ios/documentation/cocoa/conceptual/Strings/Articles/formatSpecifiers.html).

The following are equivalent:

    puts "A #{variable} goes here"
    NSLog("A %@ goes here", variable)

Just remember that any `NSLog` messages you leave in your app will be visible
to the curious, even after you've released to the App Store.