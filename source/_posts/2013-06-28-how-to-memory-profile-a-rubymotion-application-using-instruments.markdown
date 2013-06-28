---
layout: post
title: "How to Memory Profile a RubyMotion application using Instruments"
date: 2013-06-28 09:54
comments: false
categories: 
---
[Instruments](https://developer.apple.com/library/mac/#documentation/developertools/conceptual/InstrumentsUserGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40004652-CH1-SW1), to quote Apple, "is a performance, analysis, and testing tool for dynamically tracing and profiling OS X and iOS code".

Essentially it gives you live data while your application is running to
help track down memory leaks and performance issues.

## Profiling in the simulator

Simply fire your application up and then open Xcode. Open Instruments via the menu:

    Xcode > Open Developer Tool > Instruments

Once Instruments had opened, I elected to keep the icon in the dock. This just
saves having to open Xcode each time I want to run Instruments.

Once you've chosen the type of profiling you want to perform, from the `Choose Target`
dropdown select, select `Attach to Process`. Under the `System` heading you should
see the name of your app to select.

Finally hit the record button and you're good to go.

## Profiling whilst your app is running on your device

First up make sure your device is recognised by Xcode to be "used for development".

If you haven't done this, connect your device to your computer and open Xcode
and open the organiser via:

    Xcode > Window > Organizer

Find your device and select "Use for development"

Now in Instruments you should be able to select your iPhone as a target. Then under
`Choose Target` you should get a list of your apps to choose from.

Note that your application must have been provisioned using a development profile.