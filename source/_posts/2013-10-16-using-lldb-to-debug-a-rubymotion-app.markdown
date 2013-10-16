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

The last argument is the Object Reference as indicated by `message sent to deallocated instance 0x9fd56f0`.

This (hex) number is the address of a piece of memory where the object we are
interested in (was) located. The other traces in the malloc history are cases
where that same memory location was used.

Running the above command will give you a dump of information from the stack logs.
I've truncated the following example, as the information is plentiful, but it
shows the kind of output you can expect:

    malloc_history Report Version:  2.0
    Process:         YourAppName [68253]
    Path:            /path/to/YourAppName
    Load Address:    0x1000
    Identifier:      YourAppName
    Version:         ??? (???)
    Code Type:       X86 (Native)
    Parent Process:  debugserver [68922]

    Date/Time:       2013-10-16 11:14:24.104 +0100
    OS Version:      Mac OS X 10.8.5 (12F45)
    Report Version:  7

    ALLOC 0x9fd56f0-0xa64da98 [size=425]: thread_4925a28 |start | main | UIApplicationMain | -[UIApplication _run] | CFRunLoopRunInMode | CFRunLoopRunSpecific | __CFRunLoopRun | __CFRunLoopDoSource1 | __CFRUNLOOP_IS_CALLING_OUT_TO_A_SOURCE1_PERFORM_FUNCTION__ | PurpleEventCallback | _PurpleEventCallback | _UIApplicationHandleEvent | -[UIApplication sendEvent:] | ...

    FREE  0x9fd56f0-0xa64da98 [size=425]: thread_4925a28 |start | main | UIApplicationMain | -[UIApplication _run] | CFRunLoopRunInMode | CFRunLoopRunSpecific | __CFRunLoopRun | __CFRunLoopDoSource1 | __CFRUNLOOP_IS_CALLING_OUT_TO_A_SOURCE1_PERFORM_FUNCTION__ | PurpleEventCallback | _PurpleEventCallback | _UIApplicationHandleEvent | -[UIApplication sendEvent:] | ...

    ALLOC 0x9fd56f0-0xa64dacf [size=112]: thread_4925a28 |start | main | UIApplicationMain | GSEventRun | GSEventRunModal | CFRunLoopRunInMode | CFRunLoopRunSpecific | ... | rb_scope__initWithFillColor:__ | vm_dispatch | rb_vm_dispatch | builtin_ostub1(objc_object* (*)(objc_object*, objc_selector*, ...), objc_selector*, objc_object*, unsigned char, int, unsigned long*) | _objc_rootAlloc | class_createInstance | calloc | malloc_zone_calloc

The output shows there had been two mallocs in the life span of the app and only
one was freed.

If I was not expecting the first ALLOC to be freed, then I would investigate the
trace of the FREE to figure out why it was freed. Alternatively if I was expecting
the second ALLOC to be FREEd as well, then I would know that did not happen.

I knew I was expecting the memory to be freed and it was towards the end of the
second ALLOC block I could see my own method `initWithFillColor` being called.

I was subclassing `UIBarButtonItem` with my method `initWithFillColor` like so:

      class MenuButton < UIBarButtonItem

        def initWithFillColor(fillColor)
          UIBarButtonItem.alloc.initWithCustomView(button(fillColor))
        end

        private

        def showMenu
          # ...
        end

        def button(fillColor)
          UIMenuButtonIcon.alloc.initWithFrame(CGRectMake(0.0, 0.0, 25, 15), fillColor:fillColor).tap do |button|
            button.addTarget(self, action:"showMenu", forControlEvents:UIControlEventTouchUpInside)
          end
        end

      end

My `initWithFillColor` method obviously doesn't need to create another instance
of `UIBarButtonItem`. So I updated it to the following and my memory bug was gone:

    def initWithFillColor(fillColor)
      initWithCustomView(button(fillColor))
    end

Hopefully this is a good start for debugging any memory bugs in your RubyMotion
apps.

Thanks to the latest HipByte employee, [Eloy Durán](http://blog.rubymotion.com/post/62652618638/eloy-duran-joins-the-rubymotion-team), for the tips!