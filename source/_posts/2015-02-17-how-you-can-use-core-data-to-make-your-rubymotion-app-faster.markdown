---
layout: post
title: "How you can use Core Data to make your RubyMotion app faster"
date: 2015-02-17 09:05
comments: false
categories: 
---

This is my second post in a series explaining some of the technical decisions behind creating the [football management iPhone game Title Challenge](http://www.titlechallenge.com). The first post was a [short one about using Reveal App](http://paulsturgess.co.uk/blog/2015/02/13/using-reveal-app-to-build-a-rubymotion-football-manager-game/) as a useful debugging tool for building layouts.

From the outside Title Challenge may appear pretty simple, but under the hood there is a lot more than you might realise going on. Before I talk about why I chose Core Data (or even what Core Data is), let me explain a little bit about the processing the game needs to perform.

## Lots of processing means loading screens

Each ‘game week’ you progress, the following needs to be updated:

* Process player transfers (ensuring these are realistic to each club)
* Simulate player training (players improve their skill gradually over time)
* Update all injured players (each week they will be closer to full fitness)
* Update exhausted players (players get exhausted if they are not rested enough)
* Check player unhappiness (players can get unhappy at not being played enough)
* Run scout reports (your scouts will report back on your shortlisted players)
* Update the transfer list (again ensuring clubs are realistic in who they transfer list)

The key thing is that there could be 6,000+ players to update, with thousands of other related records to update in the database. I wanted this to run as fast as possible to reduce waiting times between game weeks.

While this is going on we show a loading screen. We decided to spend time making these look nice by including colourful player illustrations by [Liverpool FC illustrator Dave Will](http://davewilldesign.com/#/lfc-postcard-series-1314/).

Combined with a random humourous/entertaining/interesting quote (of which there are hundreds in the game) it makes waiting as pleasant as possible. Here's an example:

<a href="/images/loading.PNG" target="_blank">{% img left /images/loading.PNG 825 825 'Loading image' 'Loading image' %}</a>
&nbsp;
## Measuring Performance with RubyMotion-Benchmark

One way I made the code run faster was to run it, measure it, tweak it. Rinse and repeat. [Motion Benchmark](https://github.com/Watson1978/RubyMotion-Benchmark) is a very, very simple gem but it is easy to use for this very task. It gives you precise control on what code is benchmarked, but this also makes it time consuming to identify what you should be benchmarking in the first place.

## Find the bottlenecks in your code

This is where the Time Profiler in XCode’s Instruments tool is incredibly useful. It could be the subject of an entire blog post. In a nutshell it shows the exact time taken for each branch of code, even for each thread. It gives you a full stack trace to work so you can even see which line of code it is. This makes it easy to identify where the biggest bottlenecks are. I would always try and optimise those first to get some quick wins.

## Optimise your code without even making it run faster

However, the main point of this blog post is to recommend thinking about how you can optimise your code without even making it run faster. This probably sounds like a an odd statement, but this is where Core Data comes in.

Many developers think [Core Data](http://en.wikipedia.org/wiki/Core_Data) is just another way to persist data and is comparable to using [SQLite](http://www.sqlite.org/). Indeed, Core Data can use SQLite to persist data and I use Core Data with SQLite in Title Challenge. However, the fact Core Data can use SQLite is not important and you won’t be writing any SQL to use it. Core Data is primarily a framework for object life-cycle and object graph management.

Coming from a web development background it took me a while to get my head around this. I’ve seen (and written) my fair share of Rails apps tightly coupled to ActiveRecord and the database layer. Using Core Data forces you to understand that persistence is not the same thing as the object graph. So what is “object graph management”? The best way I can explain it, without loads of boring theory, is to tell you how I use it in Title Challenge.

## Core Data and the Managed Object Context

I figured out the fastest way to perform the game week processing was to do it while the gamer was not even waiting for it to finish. I decided I wanted to perform the processing while the gamer was picking their team, analysing their squad, scouring the transfer list etc. I’m effectively doing all the processing for the next game week ahead of time.

Core Data allows me to do this because I can take a copy of all the objects in the “Object Graph” ie. all the Clubs, Players etc. (in what is known as a Managed Object Context) and manipulate them as necessary on a background thread. I can perform all the tasks I mentioned earlier (e.g. processing transfers), without any of the changes showing up in the user interface as as the gamer is navigating around the app.

In fact, none of these changes are persisted to disk until the gamer hits continue. This is one of the major differences of Core Data from how you use a database in a web app. With Core Data you make as many changes as you like and at some later date you tell it to save.

Core Data is smart enough to handle the merging of all the changes from the background thread in with the changes made by the gamer on the main thread. The beauty of this technique is it doesn’t really matter how fast/slow my processing code runs, if the gamer is not even aware its running in the background.

This approach has worked really well, especially on older devices where sometimes it doesn’t matter how much you optimise your code, it’s going to run slowly.

## Some Complications

It becomes more complicated when the gamer interacts with players that have also changed in the context on the background thread. This is pretty rare in practice, but I’ve written code to handle any conflicts so it resolves them before continuing to the next game week.

The other thing to watch out for is high memory usage. I found as I was processing nearly every player and every club, each game week, these were all getting pulled into memory and not released. Core Data will hold onto objects unless you tell it let go. This is great for performance, but not so great when memory is at a premium. I highly recommend reading up on `refreshObject` in the Core Data docs. Call this method on objects when you’re finished with them to release them and control memory spikes.

## Shameless plug

If you got this far and you're still wondering what Title Challenge is or you found this post useful and just feel like supporting me – Please do check out [Title Challenge on the App Store](http://itunes.apple.com/app/id959982756).






