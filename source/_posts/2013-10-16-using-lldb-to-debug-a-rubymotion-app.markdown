---
layout: post
title: "Using LLDB to debug a RubyMotion app"
date: 2013-10-16 12:37
comments: false
categories: 
---

RubyMotion now uses [LLDB](http://lldb.llvm.org/) for debugging. Previously it
used GDB.

If you're getting random crashes with little or no backtrace it may be down to
a memory allocation issue.

Launch your application with the debugger running via:

    $ rake debug=1 NSZombieEnabled=YES MallocStackLogging=1

Trigger your bug and you should get output in Terminal like this:

    2013-10-16 10:17:19.594 YourAppName[68253:a0b] *** -[UIBarButtonItem isSystemItem]: message sent to deallocated instance 0x9fd56f0
    Process 68253 stopped
    * thread #1: tid = 0x3127cd, 0x03ac0811 CoreFoundation`___forwarding___ + 769, queue = 'com.apple.main-thread, stop reason = EXC_BREAKPOINT (code=EXC_I386_BPT, subcode=0x0)
        frame #0: 0x03ac0811 CoreFoundation`___forwarding___ + 769
    CoreFoundation`___forwarding___ + 769:
    -> 0x3ac0811:  jmp    0x3ac090c                 ; ___forwarding___ + 1020
       0x3ac0816:  movl   %edi, (%esp)
       0x3ac0819:  calll  0x3bb840e                 ; symbol stub for: class_getSuperclass
       0x3ac081e:  movl   %eax, %edi
    (lldb)

Leave your application running and open a separate tab in Terminal. You can
inspect the `malloc_history` of the object that has been deallocated via:

    /usr/bin/malloc_history 68253 0x9fd56f0

Note the first argument is the Process ID as indicated where it says
`Process 68253 stopped`. Note that the Ruby process that performs the build and
launches your app (i.e. rake) is not the same as your app process.

The last argument is the Object Reference as indicatated by `message sent to deallocated instance 0x9fd56f0`.

This will give you a dump of information from the stack logs. The output and how
you interrogate it is going to depend on the specifics of your application.

In my case it showed that, at a specific memory location, there had been two
mallocs in the life span of the app and only one was freed. You'll see this by
the number of blocks of text that start with either `ALLOC` or `FREE`.

Each block of text displays the lifetime from left to right.

If I was not expecting the first ALLOC to be freed then I would investigate the
trace of the FREE to figure out why it was freed. Alternatively if I was expecting
the second ALLOC to be FREEd as well then I would know that did not happen.

Hopefully this is a good start for debugging any memory bugs in your RubyMotion apps. Thanks to the latest HipByte employee [Eloy Durán](http://blog.rubymotion.com/post/62652618638/eloy-duran-joins-the-rubymotion-team) for the tips!