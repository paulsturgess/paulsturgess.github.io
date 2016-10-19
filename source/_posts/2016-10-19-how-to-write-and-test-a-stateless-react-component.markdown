---
layout: post
title: "How to write and test a stateless React component"
date: 2016-10-19 19:25
comments: false
categories:
---
Recently I've been learning [Redux](http://redux.js.org/) state container for [React](https://facebook.github.io/react/) apps and it actively encourages
'stateless' components.  This blog post will describe how these can be written
and tested.

Note that you can write stateless components whether you're using Redux or not!

## What is a stateless component?

Redux introduces the concept of ['presentational' and 'container' components](http://redux.js.org/docs/basics/UsageWithReact.html#presentational-and-container-components) and the major difference between the two is that your presentational components
should not be aware of Redux.

Without getting into the details of exactly how Redux works, the consequence of
this separation of responsibilities means your presentational components can be
fairly simple.

Certainly if they don't make use of any React lifecycle methods (like [componentDidUpdate]('http://paulsturgess.co.uk/blog/2016/05/04/how-to-test-react-lifecycle-method-componentdidupdate-when-the-component-props-change/')) they can start life as a
simple function that just returns the required output.

## What does a stateless component look like?

Below is an example taken from a Todo app, what it does isn't important. The
key thing to notice is that it's just a function that returns a list item.

Three props are defined `onClick`, `completed` and `text`. There's no state
manipulation.

<script src="https://gist.github.com/paulsturgess/db388c5f981775e96d8cb33d9fdcf073.js">
</script>

## So how is stateless component tested?

When using `ReactTestUtils.renderIntoDocument` it will not work with a stateless
component – you'll get an error 'instance must be a composite component'.

The way around this is to use a component created by extending `React.Component`
to wrap the stateless component. You'll see in the test below a
`Wrapper` component is created.

Once you've done that you can reach into the component to grab out specific DOM
elements or other components.

<script src="https://gist.github.com/paulsturgess/1cc50c28c6a0b622f9fddceb4d268c41.js">
</script>
