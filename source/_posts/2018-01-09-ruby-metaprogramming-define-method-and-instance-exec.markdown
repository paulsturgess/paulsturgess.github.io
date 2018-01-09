---
layout: post
title: "Ruby metaprogramming: define_method and instance_exec"
date: 2018-01-09 20:48
comments: false
categories:
---
This blog post runs through an example of iterating on some Ruby code. The bizarre fictional scenario
develops to make the requirements ever more complicated. There's certainly many different ways to
solve the underlying problem, but I wanted to show an example where `define_method` and `instance_exec`
can be combined with class methods to make for some very powerful and concise code.

For each change in requirements, I'll post the code in full for how I've handled the new scenario.

I will say upfront that these two metaprogramming methods should be used with caution. Powerful and concise code does not necessarily make for easy to understand/debug/maintain code.

## The Curry Restaurant

So, there's a curry restaurant that sells Vindaloo and Tarka Daal and there's a customer that likes to order curry from the restaurant... bear with me :). Upon receiving their meal, the customer will respond
with a comment if the spiceyness isn't to their taste. Unfortunately the restuarant isn't consistent
with the spices it uses when cooking.

<script src="https://gist.github.com/paulsturgess/f5f51b4bfa63d3ffe1833a115d9b98c3.js"></script>

With the above code we can do things like:

```ruby
spicey_customer = SpiceyCustomer.new
spicey_customer.order_tarka_daal
spicey_customer.order_vindaloo
```

If any of the meals are mild then the customer will respond with "That's too mild!".

## A new greedy customer

Then along comes another customer, a greedy one. They also like to order curry and comment on the meal. However, rather than comment on the spiceyness, they like to comment on the size of the portion.
Unfortunately the restuarant is also inconsistent with its porton sizes, but the greedy customer
doesn't care what type of curry it is, they just want it large!

<script src="https://gist.github.com/paulsturgess/47d658b90ebea41bef9c59c26a218f35.js"></script>

Already we have a bit of duplication between the two customers but it's not a massive problem at
this point.

## A new menu item - Jalfrezi

The restaurant is doing really well and introduces a new Jalfrezi curry to the menu. The greedy customer responds the same to it as for any curry, but the spicey customer responds to the  Jalfrezi,
the same as when recieving a Vindaloo - they want it hot!

<script src="https://gist.github.com/paulsturgess/2c5fe67bbba23fca6f5c7edce72adf1d.js"></script>

We've added `order_jalfrezi` to both customers and added `expect_hot_curry` to `SpiceyCustomer` because
they respond the same for jalfrezi and vindaloo. Now we really do have duplication between the two customers, so we'll look at refactoring that next.

## Refactor - Dining Out

We decide that the two customers have enough shared behaviour to introduce a new module called `DiningOut`.
This means both customers can order all three currys and then respond in a way that is specific to them. With the custom response defined in each `Customer` in the `respond_to_meal` method.

You might be thinking it would be better to make `Customer` a class and have `GreedyCustomer` and `SpiceyCustomer` as subclasses that inherit from it, but for this example it doesn't matter. This is a version of the [Template method pattern](https://en.wikipedia.org/wiki/Template_method_pattern).

<script src="https://gist.github.com/paulsturgess/dd849afecc6deea3d89e4bdcfd6fadca.js"></script>

We have our new `DiningOut` module and we've defined a `respond_to_meal` in both customers. This code is definitely cleaner but there is still a bit of a smell.

If the restaurant expands with lots of new items on the menu and these customers need to respond differently, then the `respond_to_meal` method is going to get pretty big and complicated pretty quickly. We're also
very reliant on the name of the meal not changing.

In addition the `DiningOut` module will bloat quickly with each new menu item. That isn't a massive problem, but there's already a fair bit of obvious duplication going on where the name of the order method closely matches the argument sent to `order_meal`.

Now you might be thinking, why can't we just expose `order_meal` as a public method and call `SpiceyCustomer.new.order_meal(:jalfrezi)` instead of `SpiceyCustomer.new.order_jalfrezi`. However...

## A new fussy customer

Along comes the Fussy customer who will only eat tarka daal and the newest item on the menu, chips.
The fussy customer isn't interested in responding to the meal and just eats in silence. In addition,
the Greedy customer will order chips, but the Spicey customer would never order chips. Sounds complicated doesn't it?

The simplest thing to do is to add an `order_chips` method to `DiningOut` and an empty `respond_to_meal` to the new `FussyCustomer`.

<script src="https://gist.github.com/paulsturgess/8848d1567653cd384ea7ee35450e9a1f.js"></script>

Now we have some complicated rules and they're not properly enforced. There's nothing stopping us calling `FussyCustomer.new.order_vindaloo` or `SpiceyCustomer.new.order_chips` even though neither of those customers would order those things.

We could start putting some logic into the `order_*` methods to check the type of customer before ordering the meal, but that would get ugly fast. Ideally we want a solution where we can tell by looking at the code which customer likes to eat which food and each customer wont even know how to order food they don't want to order.

## Introducing define_method and instance_exec

Before we get into the final solution, the end result is that we can now do things like the following:

```ruby
spicey_customer = SpiceyCustomer.new
spicey_customer.order_tarka_daal
spicey_customer.order_vindaloo
spicey_customer.order_jalfrezi
spicey_customer.order_chips # returns an undefined method error

greedy_customer = GreedyCustomer.new
greedy_customer.order_tarka_daal
greedy_customer.order_vindaloo
greedy_customer.order_jalfrezi

fussy_customer = FussyCustomer.new
fussy_customer.order_chips
fussy_customer.order_tarka_daal # returns an undefined method error
```

Also when we look at each customer we can see very clearly what they like to eat and how they like to respond for each menu item.

<script src="https://gist.github.com/paulsturgess/189a8ca40c7f0a0423d9235ff4ebe3fc.js"></script>

The nice thing is that if the restaurant adds a new menu item, we don't need to touch the `DiningOut`
module at all. We just need to decide which customers would like to order the new menu item and how they might optionally respond.

It works by defining a new class method `eats`. This method takes the name of the menu item and a
block to be called with the meal so the customer can inspect the meal to determine their response.

The `eats` class method in `DiningOut` uses `define_method` to dynamically create the `order_*` instance method on the customer. This means if the customer declares `eats :chips` then they will respond to the method `order_chips` (and conversely if they don't define it then they will return undefined method error). This makes it very obvious what menu items the customer will eat and prevents mistakes.

The order method uses `instance_exec` to execute the response block passed in. This is required because we want to ensure the block is called on the customer instance and not in the context of the class method - if we just did `block.call(meal)` then an undefined method (for whatever method the response block called) would be raised.

By accepting a block and calling it with `instance_exec` gives the nice benefit of that block being able to call any method it likes on the instance. In this scenario each `Customer` can call a different response method, for each type of menu item, if they want. This is important because, for example, the `Chips` menu item does not respond to the method `spiceyness` - so if there was a single assumed `respond_to_meal` method each `Customer` implemented it would have to handle every type of meal (checking if it responds to methods such as `spiceyness` and all sorts of other logic).

## Conclusion

Obviously this is a ridiculously contrived and long example to demonstrate two metaprogramming methods in Ruby but hopefully if you've read this far, you've got something out of it.

As I mentioned in the intro, these metaprogramming methods should be used with caution. Powerful and concise code does not necessarily make for easy to understand/debug/maintain code.