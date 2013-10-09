---
layout: post
title: "How to debug Core Data output to the console in RubyMotion"
date: 2013-10-09 08:18
comments: false
categories: 
---
Core Data can show you the underlying queries and execution times whilst you
are running your app. Just fire it up with:

    rake args="-com.apple.CoreData.SQLDebug 1"

Note you can bump the number up to 2 or 3 depending on how much information
you want. 3 goes as far as to show you the result set Core Data has fetched.