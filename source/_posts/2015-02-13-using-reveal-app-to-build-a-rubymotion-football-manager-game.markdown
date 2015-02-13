---
layout: post
title: "Using Reveal App to build a RubyMotion football manager game"
date: 2015-02-13 15:26
comments: false
categories: 
---

This is the beginning of a series of blog posts I intend to write on how I've built [Title Challenge – Football Management](http://titlechallenge.com).

It's an iOS game I've written in [RubyMotion](http://www.rubymotion.com/). I'm a Ruby on Rails web developer turned app developer. Before building this game, I had no prior knowledge of building mobile applications.

Hopefully this series of posts will inspire other Ruby developers out there to start building really cool mobile apps.

[Reveal App](http://revealapp.com/) was one of the first third-party tools I downloaded when I started building Title Challenge 18 months ago and I've used it nearly every day since. At first glance it might look like a bit of a gimmick to see your app in 3D but it is incredibly useful for debugging.

<a href="/images/reveal_app.png" target="_blank">{% img left /images/reveal_app.png 825 825 'Reveal App screenshot' 'Reveal App screenshot' %}</a>


I've used Reveal to show me when a `UIView` is tucked behind some other `UIView` that I couldn't see in the Simulator. Sometimes a `UIVIew` might not have a height set or something stupid like that and Reveal would instantly show me.

You can rotate your app round to see it from angles you can't see in the Simulator.

Another really useful feature is being able to tweak attributes live in the Reveal and the Simulator reflects your changes. For example, you can change the frame of a `UIView` and see it move in the simulator live.

I've got no affiliation with Reveal but I definitely recommend it for App developers.