---
layout: post
title: "Functional Programming for a Rubyist"
date: 2016-11-20 12:22
comments: false
categories:
---
Functional programming has piqued my interest for a while – it appears to be reaching
a tipping point with the rise of [ReactJS](https://facebook.github.io/react/), [Redux](http://redux.js.org/), [Elm](http://elm-lang.org/) and [ImmutableJS](https://facebook.github.io/immutable-js/)
bringing Functional Programming to a whole new set of developers through Javascript.

[Elixir](http://elixir-lang.org/) is also a functional programming language that's
attracting a lot of the Ruby community.

As an object-orientated Rubyist, I've been exposed to some functional programming
ideas through Blocks, Procs and Lambdas but when programming in Ruby you're typically
mutating state all over the place.

Pure functional programming works with immutable state.

## Coursera

To learn more about functional programming, I've just successfully completed the
Coursera course [Functional Programming Principles in Scala](https://www.coursera.org/learn/progfun1)
taught by the creator of [Scala](http://www.scala-lang.org/), Martin Odersky.

Although I didn't set out to specifically learn Scala, I decided to take the
course because the [reviews from fellow programmers](https://www.coursera.org/learn/progfun1#ratings)
highly recommended it for learning Functional Programming. I think they were right – it's
a very well put together course and it's also really tough.

It completely challenges the way you think when you are used to writing imperative code.
The course is definitely less about learning Scala and more focussed on the fundamentals
of functional programming. Interestingly Scala actually allows both the worlds of
functional and object-orientated to co-exist.

Each week you have an assignment to complete that you can upload your program directly
from your IDE – there's an automated test suite that will grade your submission.

## Side effect free

So one of the reasons functional programming is gaining popularity is because one of
the main cited benefits is that the code is easier to reason about due to being
'side-effect free'.

It's this immutability that is at the core of React & Redux, especially for performance.

When a component's props or state change, React decides whether an actual DOM update
is necessary. By using immutable state, it's very easy to track if an object
has changed. The React documentation has a really good detailed explanation on [Optimizing Performance](https://facebook.github.io/react/docs/optimizing-performance.html).

## Scalable

Functional programming languages are often talked about as being highly performant, but
what does this mean?

The future trend for computing appears to be more cores and one the way to make best
use of the hardware is to run code in parallel. This is where that 'side-effect free'
code comes into play as makes it a lot easier to run in parallel.

## The Future

Functional programming is certainly a completely different mindset for those of
us used to object-oriented programs. It will be really interesting to see if it
does become the mainstream approach to building web applications. I think I might
start trying to apply some functional programming in Ruby and see how I get on.

Further Reading...

* [Avoid mutation in Ruby](http://www.rubypigeon.com/posts/avoid-mutation-functional-style-in-ruby/)
* [Isolate side-effects in Ruby](http://www.rubypigeon.com/posts/isolate-side-effects-functional-style-in-ruby/)
* [Applying functional programming in Rails](http://blog.carbonfive.com/2014/01/26/applying-functional-programming-principles-to-your-rails-codebase/)
