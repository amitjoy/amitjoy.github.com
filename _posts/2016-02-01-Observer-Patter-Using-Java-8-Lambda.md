---
layout: post
title: "Observer Pattern using Java 8 Lambda Expression"
author: amit
modified:
share: true
comments: true
excerpt: "Implementing the observer pattern using the powerful lambda expressions"
tags: [Java, Java8, Lambda, Stream]
---

{% include _toc.html %}

## Overview

We have previously seen about implementing Command Pattern using Java 8 Lambda expressions. Now, we would focus on implementing Observer Pattern using the Functional Programming Paradigm in Java 8.

### Observer Pattern

The following image is an excerpt from **SourceMaking.com**

<figure>
	<img src="https://sourcemaking.com/files/v2/content/patterns/Observer_example1-2x.png"></a>
</figure>

### The Problem

The problem is taken from the book "Java 8 Lambdas by Richard Warburton".

Let's discuss about the problem before solving it using Java 8 Lambda Expression.

Nasa has decided to spy on the activities occurring on Moon. So, Nasa needs to build a module to check whether any asteroid or any apollo spacecraft has reached. Nasa needs an effective way so that they can change the module anytime according to their requirements. They might need to extend the module to more spying activities.

### OOP way

First of all, I would love to tell you about how it can be achieved using the object oriented way.

### Observer

{% highlight java %}
public interface LandingObserver {

	public void observeLanding(String name);

}
{% endhighlight %}

### Concrete Observer - Apollo

{% highlight java %}
public final class Nasa implements LandingObserver {
	@Override
	public void observeLanding(final String name) {
		if (name.contains("Apollo")) {
			System.out.println("We made it!");
		}
	}
}
{% endhighlight %}

### Concrete Observer - Asteroid

{% highlight java %}
public final class Aliens implements LandingObserver {

	@Override
	public void observeLanding(final String name) {
		if (name.contains("Apollo")) {
			System.out.println("They're distracted, lets invade earth!");
		}
	}

}
{% endhighlight %}

### Client

{% highlight java %}
public final class Moon {

	public static void main(final String[] args) {
    final Moon moon = new Moon();
		moon.startSpying(new Nasa());
		moon.startSpying(new Aliens());

		moon.land("An asteroid");
		moon.land("Apollo 11");
	}

	private final List<LandingObserver> observers = Lists.newCopyOnWriteArrayList();

	public void land(final String name) {
		for (final LandingObserver observer : this.observers) {
			observer.observeLanding(name);
		}
	}

	public void startSpying(final LandingObserver observer) {
		this.observers.add(observer);
	}

}
{% endhighlight %}

### Functional Way

Now let's move on to the Functional Paradigm

### Observer

{% highlight java %}
public interface LandingObserver {

	public void observeLanding(String name);

}
{% endhighlight %}

### Client

{% highlight java %}
public final class Moon {

	public static void main(final String[] args) {
    final Moon moon = new Moon();

		moon.startSpying(name -> {
			if (name.contains("Apollo")) {
				System.out.println("We made it!");
			}
		});

		moon.startSpying(name -> {
			if (name.contains("Apollo")) {
				System.out.println("They're distracted, lets invade earth!");
			}
		});

		moon.land("An asteroid");
		moon.land("Apollo 11");
	}

	private final List<LandingObserver> observers = Lists.newCopyOnWriteArrayList();

	public void land(final String name) {
		for (final LandingObserver observer : this.observers) {
			observer.observeLanding(name);
		}
	}

	public void startSpying(final LandingObserver observer) {
		this.observers.add(observer);
	}

}
{% endhighlight %}

### Benefits

Concrete Implementation of the observers have been passed as behavior which eventually made the application more concise and less bloated.
