---
layout: post
title: "Java Iterator Pattern vs Streams vs RxJava Observable"
author: amit
modified:
share: true
comments: true
excerpt: "The subtle difference between push and pull iterators"
tags: [Java, Java8, Lambda, Stream, RxJava, Reactive Programming]
---

{% include _toc.html %}

## Overview

As a Java Programmer, we all must have heard about the **Iterator Pattern**. The main idea behind **Iterator Pattern** is to traverse an **Iterable** instance. So what is so important here? The main focus on how it traverses the **Iterable** container. The **Iterator** **pulls in** the data from the container one by one. In comparison with the RxJava Observable, it doesn't pull in the data from the container, instead it pushes them as notifications.

### Iterator Pattern

{% highlight java %}
public final class IteratorPattern {

	public static void main(final String[] args) {
		final List<String> names = Lists.newArrayList("Java", "C", "C++", "PHP", "Go");
		for (final Iterator iterator = names.iterator(); iterator.hasNext();) {
			String language = (String) iterator.next();
			System.out.println(language);
		}
	}

}
{% endhighlight %}

### Java 8 Streams

Due to the evolution of functional programming paradigm, lots of new functionalities have been introduced in Java 8. Streams facilitates us with the **Internal Iteration** functionalities.

### External Iteration

Java's Iterator Pattern is inherently sequential and pulls in each and every data before it exhausts the complete container. It lacks lots of functionalities such as data parallelism, concurrency et al.

### Internal Iteration

Java Stream API provided the functionality for internal iteration to overcome the challenges confronted by External Iterators. It is more concise and readable.

{% highlight java %}
public final class ExternalIteration {

	public static void main(final String[] args) {
		final List<String> names = Lists.newArrayList("Java", "C", "C++", "PHP", "Go");
		names.stream().filter(language -> language.contains("C")).forEach(System.out::println);
	}

}
{% endhighlight %}

The afore-mentioned example tries to filter a list to print only those programming languages which contain **C** letter in it.

Isn't it pretty simple to write and understand? I believe it is.

### RxJava

But does the **Internal Iterators** follow the same **Iterator Pattern** to pull in the data from the data container?

**Yes**, it does pull in the data from the container to process. To overcome the challenge, **Netflix** has released a library **RxJava** which works on pushing the data to the consumers as notifications rather than pulling data.

In **RxJava**, each and every data container is considered as **Observable** which can be consumed by **Observers** or **Subscribers**. The **Observers** or **Subscribers** get notified with each and every element from the container. **RxJava** strictly follows the **Push** pattern to decouple the **Observable** and the **Observer**.

{% highlight java %}
public final class PushVSPull {

	public static void main(final String[] args) {
		new PushVSPull().run();
	}

	private static void pullExample() {
		final List<String> list = Lists.newArrayList("Java", "C", "C++", "PHP", "Go");

		final Iterator<String> iterator = list.iterator();

		while (iterator.hasNext()) {
			System.out.println(iterator.next());
		}
	}

	private static void pushExample() {
		final List<String> list = Lists.newArrayList("Java", "C", "C++", "PHP", "Go");

		final Observable<String> observable = Observable.from(list);

		observable.subscribe(System.out::println, System.out::println,
				() -> System.out.println("We are done!"));
	}

	public void run() {
		pullExample();
		pushExample();
	}
}
{% endhighlight %}

In the aforementioned example, you can see that we have created an **Observable** instance out of the list. Then we have subscribed to it to consume the data from the container. The **subscribe** method accepts 3 arguments. The first argument is used to trigger the behavior as soon as an element gets emitted from the container and pushed to this subscriber. The second argument is used to notify subscriber of any erroneous emission from an **Observable** container. And finally the third one is used to trigger the behavior as soon as the **Observer** finished consuming the complete **Observable** container.

###Benefits

**RxJava** mainly targets to solve the hassles of **Asynchronous/Reactive Programming** whereas Asynchronous Programming in Java 8 can only be achieved using **CompletableFuture** but using **CompletableFuture**, it is not possible to consume simple data container in reactive fashion. Apart from this, **RxJava** can also work in **Java 6** so it is possible to adapt **Reactive Programming** in old projects as well.
