---
layout: post
title: "How to test React lifecycle method componentDidUpdate when the component props change"
date: 2016-05-04 22:12
comments: false
categories:
---

[React](https://facebook.github.io/react/) has many lifecycle methods you can hook behaviour onto, one of which is `componentDidUpdate`.

This blog will show you how to test this method using [react-dom](https://www.npmjs.com/package/react-dom).

Let's say your component implements `componentDidUpdate` like this:

{% highlight javascript %}
import React from 'react';

class YourComponent extends React.Component {
  componentDidUpdate() {
    this.someFunction();
  }

  someFunction() {
  	// ...
  }
}
{% endhighlight %}

Now say you want to test that `componentDidUpdate` calls `this.someFunction()`.

Because the props for the component are set on initial render, and cannot change after that, you can't just render an instance of the component and manually update the props in your spec.

This is where [react-dom](https://www.npmjs.com/package/react-dom) comes in. Use it to render the component with an initial set of props and then render the component again with different props.

react-dom will re-render the same instance of the component, instead of creating a brand new one each time.

{% highlight javascript %}
import React from 'react';
import ReactDOM from 'react-dom';
import YourComponent from 'path/to/your-component';

describe('your-component', () => {
  describe('componentDidUpdate', () => {
    it('calls someFunction()', () => {
      let node = document.createElement('div');
      instance = ReactDOM.render(<YourComponent yourProp={'foo'} />, node);
      spyOn(instance, 'someFunction');
      ReactDOM.render(<YourComponent yourProp={'bar'} />, node);
      expect(instance.someFunction).toHaveBeenCalled();
    });
  });
});
{% endhighlight %}
