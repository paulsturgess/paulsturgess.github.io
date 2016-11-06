---
layout: post
title: "How to write and test a Redux Container Component for React"
date: 2016-11-06 20:25
comments: false
categories:
---
As a follow on from my last article about [building and testing stateless presentational components](http://paulsturgess.co.uk/blog/2016/10/19/how-to-write-and-test-a-stateless-react-component/), I'm going to explain Redux Container Components.

## What is a container component?

A container component is concerned with how things work. This typically involves
fetching data, watching the store for updates and updating the store itself by
dispatching actions.

Essentially a container component will wrap a presentational component to add
behaviour to it.

## What does a container component look like?

<script src="https://gist.github.com/paulsturgess/7089b2386e8b86335fa7b681e3935c7a.js"></script>

The container component is created on Line 16 via the `connect()` function provided
by Redux.

You can pass in additional functions to control how you want the state and the
actions mapped to the props. These are `mapStateToProps()` and `mapDispatchToProps()`.

In the example above, I'm using `mapDispatchToProps()` to setup the `addTodo` prop
that will dispatch the `addTodo` action we've imported. How this is used is entirely
down to the `AddToForm` presentational component and is outside the responsibility
of this component.

Note the first argument to `connect()` is null because I'm not using `mapStateToProps()`, but
the principle is the same as for `mapDispatchToProps()`.

## So how is a container component tested?

Container components should be fairly lightweight. There seems little point testing
the `connect()` function, as this is not our code. But we do care that the props are
going into our wrapped `AddTodoForm` component correctly.

So to test this component, all I'm interested in is that the `addTodo` prop has
been setup to dispatch the required action to the store.

<script src="https://gist.github.com/paulsturgess/e0f06a12821343d7854718c34394b60e.js"></script>

The mock store and the `Provider` are required to render the connected component.

After that I'm just reaching into the component to grab the `addTodo` prop and calling
the function. I'm spying on the dispatch method in the store, that means I can check it's
called with the action that's formed correctly.

My test for the presentational component `AddTodoForm` will take care of checking
that the prop is actually triggered when it's supposed to.
