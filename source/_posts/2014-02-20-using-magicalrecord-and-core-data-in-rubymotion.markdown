---
layout: post
title: "Using MagicalRecord and Core Data in RubyMotion"
date: 2014-02-20 13:36
comments: false
categories: 
---
[MagicalRecord](https://github.com/magicalpanda/MagicalRecord) is a wrapper around Apple's [Core Data Framework](http://en.wikipedia.org/wiki/Core_Data). Written in Objective-C,
it's one of the most popular and mature libraries for working with Core Data.

I really like it as it simplifies a lot of the code, whilst
it still allows you to 'get your hands dirty' when necessary.

This article details how I'm using it. This is by no means the 'perfect' solution,
as I am evolving it all the time, but it is working well for me. By all means
get in touch if you think I'm missing any obvious tricks.

One thing I have found is that it's pretty much impossible to hide the fact you
are using CoreData. Particularly with the requirement of a different context for each
thread. But what I do want to do is make things as simple, consistent and maintainable
as possible.

## Installation

At the time of writing I'm using the [MagicalRecord 3.0 branch](https://github.com/magicalpanda/MagicalRecord/tree/release/3.0),
installed via [motion-cocoaopds](https://github.com/HipByte/motion-cocoapods). My `Rakefile` includes:

```
app.pods do
  pod 'MagicalRecord', :git => 'https://github.com/magicalpanda/MagicalRecord.git', :branch =>'release/3.0'
end
```

## Wrapping MagicalRecord

First I have a `Database` class to wrap common MagicalRecord tasks.

The main reason for this class is so that I do not sprinkle MagicalRecord calls
all around my code. Having them all in one place will make it easy to update if/when
the MagicalRecord api changes. It also encourages consistency in my usage of MagicalRecord
as, for example, there are many ways to persist your data.

```
class Database

  def self.filename
    "YourApplicationName.sqlite"
  end

  def self.path
    File.join(
      NSSearchPathForDirectoriesInDomains(NSApplicationSupportDirectory, NSUserDomainMask, true),
      "YourApplicationName",
      self.filename
    )
  end

  def self.created?
    File.exist?(self.path)
  end

  def self.loadOrCreate
    MagicalRecord.setupAutoMigratingCoreDataStack
  end

  def self.createTestDB
    MagicalRecord.setupCoreDataStackWithInMemoryStore
  end

  def self.delete
    if self.created?
      self.cleanUp
      File.delete(self.path)
    end
  end

  def self.reset
    Database.delete
    Database.loadOrCreate
  end

  def self.cleanUp
    MagicalRecord.cleanUp
  end

  def self.defaultLocalContext
    MagicalRecordStack.defaultStack.context
  end

  def self.backgroundLocalContext
    NSManagedObjectContext.MR_confinementContextWithParent(defaultLocalContext)
  end

  def self.save_specific_context(localContext, callback = nil)
    localContext.MR_saveToPersistentStoreWithCompletion(
      lambda { |success, error|
        NSLog("success: %@", success)
        if success
          callback.call if callback
        else
          NSLog "Error saving Seed Data"
          NSLog("description: %@", error.description)
        end
      }
    )
  end

  def self.save_test_db!
    defaultLocalContext.MR_saveToPersistentStoreAndWait
  end

  def self.save_on_main_thread!(callback = nil)
    defaultLocalContext.MR_saveToPersistentStoreWithCompletion(
      lambda { |success, error|
        NSLog("success: %@", success)
        if success
          callback.call(defaultLocalContext) if callback
        else
          NSLog "Error saving Core Data"
          NSLog("description: %@", error.description)
        end
      }
    )
  end

  def self.save_on_background_thread!(callback = nil, completion_callback = nil)
    MagicalRecord.saveWithBlock(
      lambda { |localContext|
        callback.call(localContext) if callback
      },
      completion: lambda { |success, error|
        NSLog("success: %@", success)
        if success
          completion_callback.call if completion_callback
        else
          NSLog "Error saving Core Data"
          NSLog("description: %@", error.description)
        end
      }
    )
  end

end
```

In `AppDelegate` `didFinishLaunchingWithOptions` I call `Database.loadOrCreate`. As the name implies, this
will either load my existing Core Data stack or it will set up a new one.

I also cleanup the database when my app closes via this method in the `AppDelegate`:

```
  def applicationWillTerminate application
    Database.cleanUp
  end
```

## Entities

I create my Core Data entities in Xcode (although I do intend to look at the [ruby-xcdm gem](https://github.com/infinitered/ruby-xcdm) so I can stop using Xcode).

For now, the [ib gem](https://github.com/yury/ib) is great for allowing us to fire up Xcode just when we need it.
The gem is mostly geared around using Interface Builder but I don't use it for that.

Once you've installed the gem run:

    $ rake ib:open

This will open Xcode. Inside the Resources folder on the left hand side there
will be a `.xcdatamodeld` file. Select this and you can create your entities.

One thing to remember is to set the `Class` of each Entity to the corresponding `Class`
in your app. Otherwise it will be a standard `NSManagedObject`.

So for each Entiy I create a corresponding class like so:

```
class ToDoItem < NSManagedObject
    # custom methods go here
end
```

I give each Entity `created_at` and `id` attributes in order to help with querying.

## ActiveRecord Style Behaviour

The next key part is a module I use to give behaviour to all my `NSManagedObject` classes.

```
module ModelSupport

  def self.included(base)
    base.extend ClassMethods
  end

  def switch_context(localContext)
    self.MR_inContext(localContext)
  end

  module ClassMethods
    def defaultContext
      Database.defaultLocalContext
    end

    def all(context = nil)
      localContext = context || defaultContext
      self.MR_findAllSortedBy("created_at", ascending:true, inContext: localContext)
    end

    def first(context = nil)
      localContext = context || defaultContext
      self.MR_findFirstOrderedByAttribute("created_at", ascending:true, inContext: localContext)
    end

    def last(context = nil)
      localContext = context || defaultContext
      self.MR_findFirstOrderedByAttribute("created_at", ascending:false, inContext: localContext)
    end

    def count
      self.MR_numberOfEntities
    end

    def find(id, context = nil)
      localContext = context || defaultContext
      self.MR_findFirstByAttribute("id", withValue:id, inContext: localContext)
    end

    def new(attributes)
      self.build(attributes)
    end

    def build(attributes = {}, context = nil)
      localContext = context || defaultContext
      model = self.MR_createInContext(localContext)
      model.setValuesForKeysWithDictionary(attributes)
      model.created_at = Time.now
      model
    end
  end

end
```

Essentially this class gives me ActiveRecord like behaviour to my `NSManagedObject` classes.

Also, like my `Database` class, it ensures I contain some more `MagicalRecord` calls
to a single place.

To prevent repeating the inclusion of the module all over the place I just open up the `NSManagedObject`
class and include it like so:

```
class NSManagedObject
  include ModelSupport
end
```

This means I can do:

    ToDoItem.build(
      {
        id: 1,
        action: "Lorem ipsum dolor sit amet"
      }
    )

My build method automatically inserts a `created_at` for every new record.

## Data store

By default `MagicalRecord.setupAutoMigratingCoreDataStack` will use SQLite to
persist your data.

Note that I don't include a `save` method in `ModelSupport`. Saving an individual
record isn't the most efficient way to persist changes in Core Data. Everything is held
in memory until the relevant context is told to save. Hence why I've put the
`save` method in the `Database` class.

## Persisting data on the main thread

If you've used any of the ModelSupport methods like `find`, `all`, or `build`
without passing in a context then using `Database.save_on_main_thread!` will persist any
changes made. For example:

```
new_item = ToDoItem.build(
  {
    id: 2
    action: "Some other todo item"
  }
)
Database.save_on_main_thread!
```

## Persisting data on background threads

However, if updates are made on a background thread, then we need a new (temporary)
context which is handled by `Database.save_on_background_thread!`.

For example:

```
Database.save_on_background_thread!(
  lambda { |localContext|
    new_item = ToDoItem.build(
      {
        id: 2
        action: "Some other todo item"
      },
      localContext
    )
  }
)
```

Alternatively if you don't want to wrap everything in the block. Create a
`localContext` and hold on to it. Make your changes and then save later down the line.

```
localContext = Database.backgroundLocalContext
new_item = ToDoItem.build(
  {
    id: 2
    action: "Some other todo item"
  },
  localContext
)
Database.save_specific_context(localContext)
```

Note most of the class methods in `ModelSupport` take an optional context so they
can be used on background threads.

## Testing

For tests I use the in memory data store and when saving it is synchronous. This ensures the data is there before the assertions!

For example:

```
describe "SomeTest" do

  before do
    Database.cleanUp
    Database.createTestDB
    @localContext = Database.defaultLocalContext
  end

  describe "some_method" do

    context "when some scenario" do

      it "should assign something is true" do
        # ...
      end

    end

  end

end
```

