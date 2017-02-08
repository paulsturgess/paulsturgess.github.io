---
layout: post
title: "Making and testing Ajax requests with Axios in a Redux app"
date: 2017-02-08 19:08
comments: false
categories:
---
This article is going to explain how to perform and test Ajax requests in a React
application using Redux and [Axios](https://github.com/mzabriskie/axios) which
is a promise based HTTP client.

I like Axios because of the way you can centrally configure the common behaviour
for the requests your front-end needs to make and the responses it receives.
To take one example, it has the concept of [interceptors](https://github.com/mzabriskie/axios#interceptors)
that allow you to write a single function that can the handle responses such
as a 401 unauthorized or 500 exception.

When using Axios I like to create a utility 'Service' class to setup the centralised
config and ensure all Ajax requests get handled consistently.

It also means calls to Axios are not sprinkled all over the codebase, so I could
swap out Axios for an alternative library and I should only need to update this
one wrapper class.

<script src="https://gist.github.com/paulsturgess/ebfae1d1ac1779f18487d3dee80d1258.js"></script>

In a Redux application, actions are purely functional and thus 'side-effect free'. So in order to perform Ajax requests you will need middleware such as ['redux-thunk'](https://github.com/gaearon/redux-thunk). An alternative, that I've not used but heard good things of, is [redux-loop](https://github.com/redux-loop/redux-loop).

Redux Thunk means you can return a function from your action to delay the dispatch
of it, or only dispatch if a condition is met (like the response status is 200).

Once Redux Thunk is installed, you can return a function in your Action as shown by
`saveResource` in the example below:

<script src="https://gist.github.com/paulsturgess/2335a83355a22a6d06bf72d2f462a776.js"></script>

Then for any containers that you want to use the action, you need to pass through the dispatch to the function in the `mapDispatchToProps`. See `saveResource` below:

<script src="https://gist.github.com/paulsturgess/dc495342470f006a6c60a97d29584456.js"></script>

To test the action you can use standard jasmine spy functions:

<script src="https://gist.github.com/paulsturgess/adcc72f752fa5381bc0ca22c0be91c32.js"></script>

Then finally to test the Service class:

<script src="https://gist.github.com/paulsturgess/f1813e0cee2d39ea3b57ca155ec7fee7.js"></script>

There are a few bits here to go through here. Firstly I found that when running the specs
I needed to add the [babel-polyfill](https://www.npmjs.com/package/babel-polyfill) for Promises. I'm using [Karma test runner](https://karma-runner.github.io/1.0/index.html) so I needed to add `'node_modules/babel-polyfill/dist/polyfill.js'` to the files array in the karma.config.js
file.

Secondly at the top of the spec you'll notice that [jasmine-ajax](https://github.com/jasmine/jasmine-ajax) is imported. This is the official plugin for faking Ajax requests in your Jasmine specs. What I like about this
is that it has no knowledge of Axios. In the `beforeEach` it is turned on and
then all Ajax requests triggered in the specs are captured and can be inspected.

Due to the asynchronous nature of the promise, I use `setTimeout` to grab the
most recent request and check it was formed correctly. To test the callback
I use the `then` function on the promise under test.

A small, but important, detail is the call to `done()` – this ensures the test
does not exit early and waits until the asynchronous part of the test is complete
before exiting.

And that's it! There's a lot of code there, but once the Service class is setup it
should remain pretty much unchanged. Adding additional Ajax requests to your React application actions should be fairly straight forward.
