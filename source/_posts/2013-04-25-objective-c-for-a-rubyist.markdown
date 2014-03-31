---
layout: post
title: "Objective-C for a Rubyist"
date: 2013-04-25 21:04
comments: false
categories:
---

[RubyMotion](http://www.rubymotion.com/) does a great job of allowing iOS apps to be written in Ruby. However,
whilst I want to avoid writing Objective-C, I do want to learn how to read it.

- It'll make it easier to learn Apple's SDK
- I want to be able to read existing iOS books/articles/stackoverflow solutions
- It just seems like a good idea

Fortunately both languages stem from Smalltalk, so they're not a million miles
apart.

## Instance variables

    - (void)instanceVariable;

- `-` means instance.
- next is the return type: `void` which means it returns nothing.
- Finally we have the method name, in this case `instanceVariable`.

Everything in Objective-C is camelCase. The common idiom in
 RubyMotion is to write Ruby in camel case where you are implementing
 classes that use Apple's Objective-C methods. Reverting back to snake_case when
 the class is pure Ruby.

## Class methods

    + (User *)userWithName:(NSString *)name;

- `+` means class method
- next is the return type. In this case a `User` pointer.
- next is the method name: `userWithName`
- next is a colon `:`, which means there is an argument.
- next, in brackets, is the argument type. A pointer to an `NSString` instance.
- Finally the argument variable `name`

So just be clear, in Ruby this is:

    def self.userWithName(name)
    	#...
    end

## Sending messages

As with all Smalltalk based languages, you are essentially creating methods in
classes to send messages around. I believe Objective-C does have a dot notation,
like Ruby, but most code examples I've come across use spaces and lots of square
brackets.

For example in Objective-C sending the `logOut` message to a `user` object is:

    [user logOut];

The Ruby equivalent:

    user.logOut

To chain methods in Objective-C it looks like:

    NSMutableArray *myArray = [[NSMutableArray alloc] init];

This is equivalent to the Ruby:

    myArray = NSMutableArray.alloc.init

## reallyLongMethod:WithNamedParameters:

In Objective-C named parameters are heavily used. This is great for clarity, as
they are intended to be as descriptive as possible. Removing the ambiguity is
really good, but it is important to know that named parameters are actually
included in the method name.

So an example Apple SDK method is `tableView:numberOfRowsInSection:`. Where the
colons indicate an argument is required. So in this case, two. In Objective-C it
looks like:

    - (Integer) tableView(tableView, numberOfRowsInSection:section)

RubyMotion currently runs on Ruby 1.9.3, so named arguments have been added
especially. The above method looks like the following in RubyMotion:

    def tableView(tableView, numberOfRowsInSection: section)
      # ...
    end

This can look a bit strange at times when the Apple SDK requires you to implement
methods that start with the same name, but take different arguments. If you are
not aware that the named named arguments are used in the method name, it actually
looks like you're implementing the same method multiple times.

For example, another Apple SDK method `tableView:editingStyleForRowAtIndexPath:` looks very similar in RubyMotion:

    def tableView(tableView, editingStyleForRowAtIndexPath: indexPath)
      # ...
    end

## In summary

Objective-C is definitely more verbose, but I really like the RubyMotion
implementation. There's loads more to get my head around, but this is a good
starting point.
