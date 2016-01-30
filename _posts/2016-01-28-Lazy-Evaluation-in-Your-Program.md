---
layout: post
title: "Java Lazy Evaluation"
author: Amit Kumar Mondal
modified:
excerpt:
tags: [Java 8, lambda]
---

{% include _toc.html %}

Just few days back, I stumbled upon an article in DZone about implementing Lazy Evaluation in your current program. Those of you who are quite acquainted with Java 8 Higher Order Functions or Lambda Expressions, might have already used stream based Higher Order Functions which facilitates us with the opportunity for Lazy Evaluation.

## Overview

I would like to focus on how to convert your current code to evaluate the functions lazily.

### What is Lazy Evaluation?

Lazy evaluation can be considered as the computation is delayed util it is actually needed.

### Example

The following example is slightly modified in accordance with more readability.

{% highlight java %}
public final class Lazy<T> {

  public static <T> Lazy<T> of() {
		return new Lazy<>();
	}

	private volatile T value;

	private Lazy() {
	}

	private synchronized T compute(final Supplier<T> supplier) {
		return Optional.ofNullable(this.value).map(value -> checkNotNull(supplier.get())).get();
	}

	public T get(final Supplier<T> supplier) {
		final T result = this.value;
		return Optional.ofNullable(this.value).map(value -> result).orElse(this.compute(supplier));
	}
}
{% endhighlight %}

The client application:

{% highlight java %}
public final class LazyExample {

	public static void main(final String[] args) {
		final Lazy<Candidate> lazyCandidate = Lazy.of();
		final Candidate candidate = lazyCandidate.get(() -> new Candidate("AMIT"));
		System.out.println(candidate);
	}

}
{% endhighlight %}
