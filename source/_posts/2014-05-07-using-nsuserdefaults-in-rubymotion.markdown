---
layout: post
title: "Using NSUserDefaults in RubyMotion"
date: 2014-05-07 09:19
comments: false
categories:
---
Apple describe [NSUserDefaults](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/nsuserdefaults_Class/Reference/Reference.html) as "a programmatic interface for interacting with the defaults system".

It also provides a convenient store for persisting and retrieving user preferences for your application. You can think of it as a `key` `value` hash like structure, although it is a bit smarter than that.

Here's a really simple implementation I've been using:

```
class Cache

  class << self

    def set_default(key, data)
      return if read(key)
      write(key, data)
    end

    def write(key, data)
      NSUserDefaults.standardUserDefaults[storage_key(key)] = data
      NSUserDefaults.standardUserDefaults.synchronize
    end

    def read(key)
      NSUserDefaults.standardUserDefaults[storage_key(key)]
    end

    private

    def storage_key_prefix
      "#{cache_namespace}_"
    end

    def storage_key key
      "#{storage_key_prefix}#{key}"
    end

    def cache_namespace
      raise NotImplementedError
    end

  end

end
```

```
class UserSearchSettings < Cache

  class << self

    private

    def cache_namespace
      "user_search"
    end

  end

end

```

I use it like this:

```
UserSearchSettings.write("sort_by", "name")
=> true
NSUserDefaults.standardUserDefaults.dictionaryRepresentation
=> {"user_search_sort_by" => "name"} # note there will be a lot of other key values in the hash
UserSearchSettings.read("sort_by")
=> "name"
```


My intention is not to call the `Cache` class directly and to only use the subclass `UserSearchSettings`. This forces me to group settings with a prefix defined by the `cache_namespace` method. This will help avoid accidentally defining the same key name for different values. It also nicely groups related settings.

Each application has its' own `NSUserDefaults.standardUserDefaults` so there is no need to prefix the keys with the application name.

This store will persist until the user removes your application from their device.
