---
layout: post
title: Reflux V6 Upgrade Gotchas
comments: true
---

As part of an upgrade to [React 16](https://reactjs.org/blog/2017/09/26/react-v16.0.html) in a large app I work on, I also had to upgrade, amongst other libraries, [Reflux](https://github.com/reflux/refluxjs) - the state management library we use - from version 0.2 to 6.4.

I'm not a big fan of Reflux in general, and would generally opt to use Redux in new projects, but we were already tied to it on this project.

As Reflux is less popular than Flux and Redux, there isn't as much info online. So when you run into problems, you get stuck - or at least I did. In this post I want to share some info about a couple of pitfalls I ran into, in the hope it might help others.

-----

## The Store/Component connection

In the previous version of Reflux, we were connecting our *Components* to the *Stores* using a decorator:

{% highlight javascript linenos %}
@mix.decorate(Reflux.connect(exampleStore))
export default class Example extends React.Component {
{% endhighlight %}

One thing I found somewhat confusing at first was that, if you continue to use the above mechanism to connect a *Store*, it still *kind of* works in Reflux v6 in the sense that the *Store* state object will become mixed in with the *Component* state object. 

Ie, if your exampleStore had a state object:

{% highlight javascript linenos %}
this.state = {
    isAnExample: true
}
{% endhighlight %}

...then the general idea in Reflux is that the connected *Component* would also contain the variable, ie: `this.state.isAnExample`.

In Reflux 6, with the above code and decorator, you would get this in your *Component* state: `this.state.exampleStore.isAnExample`. So the connection seems to be working, but different. The only issue is that (as far as I could tell), your *Component* will no longer re-render after the connected *Store* sends a `trigger` event (which basically means the connection is not working and is useless).

I'm not sure whether you are supposed to still be able to use the decorator approach from above in this version of Reflux - in React 16 [mixins are deprecated](https://reactjs.org/blog/2016/07/13/mixins-considered-harmful.html) (but not removed). But as far as I can tell, it doesn't work.

The new method for connecting a *Component* and *Store* looks like this:

{% highlight javascript linenos %}
export default class Example extends Reflux.Component {
    constructor(p) {
        super (p);
        this.stores = [exampleStore];
    }
}
{% endhighlight %}

So we have to 1) extend Reflux.Component instead of React.Component and 2) list the connected *Stores* in the *Component* constructor. Then it goes back to working like the previous version of Reflux did with the state mixing (ie, the exampleStore state variables will be under `this.state`, rather than `this.state.exampleStore`).

## The gotcha

All pretty straightforward. The gotcha for me here is that I was finding **certain *Components* were now connected to *Stores*, and others were not**.

## The solution

After trimming down components that did and didn't work down to their bare bones, I finally figured out my issue:

{% highlight javascript linenos %}
  componentWillMount ()
    super.componentWillMount()
    ... code ...
    }
  componentWillUnmount ()
    super.componentWillUnmount()
    ... code ...
    }
}

{% endhighlight %}

Because we're now extending Reflux.Component, which implements the `componentWillMount` and `componentWillUnmount` methods, **we have to call the super methods**. If we don't, **the *Store*-*Component* connection will FAIL - silently** - and we'll be left wondering why it's not working.

The reason for this is actually documented well in [the main README of the Reflux docs](https://github.com/reflux/refluxjs/blob/master/docs/components/README.md#componentwillmount-and-componentwillunmount), although I would have found it a lot quicker if they'd put it in the section about ["Hooking stores to components"](https://github.com/reflux/refluxjs/tree/master/docs/components)...

Happy Refluxing!

{% include twitter_plug.html %}