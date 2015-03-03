---
layout: post
title: "Using PaintCode to dynamically create images in the iOS football manager game Title Challenge"
date: 2015-03-03 15:26
comments: false
categories: 
---
One of the important and distinctive visual aspects of [Title Challenge](http://www.titlechallenge.com) is for each club to have their own shirt and colour theme. The home and away shirts are used on nearly all screens, with the front of the shirts representing a club and the rear of the shirts, with a number on the back, representing a player.

There are over 250 clubs in the database that ships with the game. Each club has 2 shirts and we need several different sizes for different screens. We also had to consider that club shirts will potentially change in both colour and style each season going forward. On top of this, we wanted to support different device resolutions including both retina and non-retina resolutions.

To create a fixed set of graphics of thousands of combinations in varying sizes, styles and colours would have been laborious to say the least and incredibly time consuming to maintain. It also would have enormously bloated the file size of our application.

## Shirt Styles

We decided we wanted to be able to dynamically display, hide, colour and scale graphical components with code. This would allow us to display any club shirt on the fly.

The first phase was to decide on some core shirt styles that covered the majority of shirts worn by football clubs. We came up with 6: Plain, Stripes, Hoops, Sleeves, Quarters and Halves. We also have a goalkeeper shirt and a substitute bib used for players on the bench.

<a href="/images/shirt_styles.png" target="_blank">{% img left /images/shirt_styles.png 825 825 'Shirt Layers image' 'Shirt Layers image' %}</a>

The image above shows the different shirt styles in Title Challenge

## Vector Layers

After deciding on the styles, Thom, our designer, produced a set of modular vector graphics that could be layered over each other to create any kit style required without duplicating any of the kit elements.

However, those vector graphics weren’t used directly in the game. The shirts you see are entirely generated from code. To achieve this, each individual shirt element was imported into a third party application called [PaintCode](http://www.paintcodeapp.com/).

[PaintCode](http://www.paintcodeapp.com/) is genius, it automatically generates drawing code from vector graphics.

<a href="/images/shirt_layers.png" target="_blank">{% img left /images/shirt_layers.png 825 825 'Shirt Layers image' 'Shirt Layers image' %}</a>

The image above shows the layers that make up a black and white striped shirt with a substitute bib.

## Code Layers

The code to generate a shirt is actually composed of the same graphical modular layers composed on top of one another, but instead of vector graphics, they’re CALayers composed of UIBezierPath line segments with different fills and strokes.

Even the shirt numbers have been generated using PaintCode. We could have used a font, and we did look for one, but we couldn’t quite find the right one. We wanted one loosely inspired by the [Mexico ‘86 world cup](http://goo.gl/nCCUqC), but with a modern feel.

In the end it was easier to implement all elements of the shirt using the same code-based method. This was particularly true when we were dealing with scaling and animations on the tactics screen (shown below).

<a href="/images/tactics.png" target="_blank">{% img left /images/tactics.png 825 825 'Tactics screenshot' 'Tactics screenshot' %}</a>

&nbsp;&nbsp;

## Turn it off and on again

To generate the front or rear of a shirt, we simply show/hide a different collar style. We also show/hide the player number as appropriate. The goalkeeper shirt is just a plain shirt with some extra sleeves added on. For bibs, we simply add the bib layer straight over the top.

Basically we just turn layers on and off and change the colours to produce the shirt combination we want.

Taking this approach means that making user interface tweaks are a joy to implement and not a chore to dread. Another benefit is we were able to include the shirts as part of data editor in the game. So any shirt style can be created completely on the fly using a color picker.

We also changed the shirt style several times during the development of Title Challenge. I have no affiliation with [PaintCode](http://www.paintcodeapp.com/), but I'm so glad we used it!

## Shameless plug

If you got this far and you&#8217;re still wondering what Title Challenge is or you found this post useful and just feel like supporting me – Please do check out [Title Challenge on the App Store](http://itunes.apple.com/app/id959982756)

I've also written some other posts about developing Title Challenge: [Debuging layout issues with RevealApp](http://paulsturgess.co.uk/blog/2015/02/13/using-reveal-app-to-build-a-rubymotion-football-manager-game/) and [How to use Core Data to make your app faster](http://paulsturgess.co.uk/blog/2015/02/17/how-you-can-use-core-data-to-make-your-rubymotion-app-faster/).
