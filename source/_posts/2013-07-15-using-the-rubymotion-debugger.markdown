---
layout: post
title: "Using the RubyMotion debugger"
date: 2013-07-15 13:47
comments: false
categories: 
---
If you haven't read [The Official RubyMotion Debugging guide](http://www.rubymotion.com/developer-center/articles/debugging/) then I'd recommend starting there. However, this is a summary of useful commands I tend to use.

Launch your application with the debugger running via:

    $ rake debug=1

Your app will pause, take the oppportunity to setup the debugger or just continue with:

    $ c

When your app crashes you can view the backtrace via:

    $ bt

I had an error that recommended setting a breakpoint in `malloc_error_break`.

This can be achieved either on the fly by:

    $ break malloc_error_break

Enter `y` to "make breakpoint pending on future shared library load"

Alternatively, a handy way of setting this up for all apps is to create a `.gdbinit` file:

    $ vim ~/.gdbinit

With the line:

    fb malloc_error_break

Now when you launch via `rake debug=1` the `malloc_error_break` breakpoint will
automatically be setup and ready to use.

## NSZombies

Running your app with NSZombies will reveal even more debugging info.

Launch your app via:

    rake debug=1 NSZombieEnabled=YES MallocStackLoggingNoCompact=1

If you get a problem with a specifc instance you can reveal more info via:

    info malloc-history <your-object-ref>

