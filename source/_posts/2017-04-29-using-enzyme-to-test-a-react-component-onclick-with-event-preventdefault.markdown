---
layout: post
title: "Using Enzyme to test a React component onClick with event preventDefault"
date: 2017-04-29 20:32
comments: false
categories:
---
[Enzyme](https://github.com/airbnb/enzyme) is a React Testing utility written
by the developers at Airbnb that is designed to make testing your components
much simpler and easier.

Here's a quick tip for testing `onClick` functions where your code calls `event.preventDefault()`

When triggering a click via the Enzyme api method `simulate`, you are not actually triggering a real event – the underlying implementation is simply calling the `onClick` prop of the node.

So if you're calling `event.preventDefault()` in your code, you will need to pass
a second argument to `simulate`, which is a mock object, that is passed through to the event handler.

For example:

```
const wrapper = shallow(<YourComponent />);
wrapper.find('a').simulate('click', { preventDefault() {} });
```
Easy when you know how, but not immediately obvious. Fortunately the [Enzyme docs](https://github.com/airbnb/enzyme/blob/master/docs/api/ShallowWrapper/simulate.md) are brilliant and well worth checking out for further detail and examples.
