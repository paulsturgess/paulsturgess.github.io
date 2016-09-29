---
layout: post
title: "Elixir London Conference 2016"
date: 2016-09-29 12:35
comments: false
categories:
---

Last week the first ever [Elixir conference](http://www.elixir.london/) was held in London and I was lucky enough to attend. It was brilliantly run with lots of great speakers and they've even got the [videos up online](http://www.elixir.london/#speakers) already!

## Elixir and Ruby

[Elixir](http://elixir-lang.org/) is a relatively new programming language that's gaining in popularity fast, particularly amongst Ruby developers. During the conference they asked the audience to raise their hand if they had been, or are, a Ruby developer and over 75% did (myself included)!

So what's attracting the Ruby community? Aside from the fact developers love shiny new things, this shiny new thing is different. That's because Elixir runs on the [Erlang](http://www.erlang.org/) BEAM which has been around for decades and was originally developed by Ericsson.

The key-note was from co-creator of Erlang,[Robert Virding](http://www.elixir.london/2016/robert-virding). It was fascinating to hear him talk about how Erlang was developed to solve the challenges Ericsson was facing building telecoms systems in the late 80's, early 90's. It's amazing that the problems they solved are just as relevant in todays world of online software applications. One of the most high-profile recent adopters of Erlang is [WhatsApp](https://www.whatsapp.com/).

So what does this have to do with Ruby? Well Elixir inherits all of the characteristics that Erlang is known for:

- Concurrent
- Fault tolerant
- Easy to scale

Often these are the same criticisms levelled at Ruby and Elixir provides an appealing syntax that looks a lot like Ruby.

## Phoenix

The icing on the cake for Rails developers looking to transition to Elixir is [Phoenix](http://www.phoenixframework.org/). Phoenix takes a lot of the good ideas in Rails and adds all the benefits of Elixir such as the concurrency and fault tolerance.

[Gary Rennie](http://www.elixir.london/2016/gary-rennie) (core member of the Phoenix team) did a great job running through some best practices with controllers in Phoenix. He also explained why umbrella applications are a good idea to logically split your application where it makes sense.

As an aside, I recently came across this article from the creator of phoenix Chris McCord [comparing Phoenix channels and Rails action cable](https://dockyard.com/blog/2016/08/09/phoenix-channels-vs-rails-action-cable) which makes for eye-opening reading.

## Nerves

However, it's not all about web applications and there were a few great talks and demonstrations showcasing embedded systems with Elixir. One of the most interesting discoveries for me was the [Nerves Project](http://nerves-project.org/).

The goal of Nerves is it make it simple for developers to write Elixir to interface with network and input/output devices, embed the code on a memory card, and run it on hardware like a [Raspberry Pi](https://www.raspberrypi.org/).

[Ju Liu's live coding demo](http://www.elixir.london/2016/tetiana-dushenkivska) involved lots of various sensors (including lasers) and was a particular highlight.

## What's next?

Elixir is very young and there's clearly lots of enthusiasm in the community. I'm definitely going to be keeping a close eye on the ecosystem that is already growing very quickly. There seems to be a lot of benefits to using Elixir for modern application development.

I definitely want to try writing an Elixir application and wrapping my head around functional programming (when I'm used to Object Oriented programming) is an interesting challenge I've been looking for an excuse to take on!
