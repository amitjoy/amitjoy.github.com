---
layout: post
title: "Reification in Java Try Catch Block"
author: amit
modified:
share: true
excerpt: "How Java catch block catches a given type at run-time?"
tags: [Java]
---

{% include _toc.html %}

## Overview
In Java's try statement, each and every catch block catches the thrown exception by matching the exact type which is thrown. The exact type thrown should be reifiable to get it caught using Try-Catch statement.

### Reifiable Types

Reifiable type is a type which can be completely represented at run-time.

Eg, A primitive type, non-parameterized class or interface type, parameterized type of unbounded wildcard ({% highlight java %}List<?>{% endhighlight %}), raw type (List), Array whose component is reifiable ({% highlight java %}List<?>[], int[], List[]{% endhighlight %})

### Typed Exception

{% highlight java %}
public final class CommunicationException<T> extends Exception {

	/**
	 * Serialiazation UID
	 */
	private static final long serialVersionUID = 7976648916518255157L;

	private final T value;

	public CommunicationException(final T message) {
		value = message;
	}
}
{% endhighlight %}

The afore-mentioned code will result in the following error.

{% highlight java %}
The generic class CommunicationException<T> may not subclass java.lang.Throwable
{% endhighlight %}

### Restriction

The user might need to write such an extension to handle it the following way.

{% highlight java %}
public final class Client {

	public static void main(final String[] args) {
		try {
			throw new CommunicationException<String>("HTTP Communication Failure");
		} catch (final CommunicationException<String> communicationException) {
			Throwables.propagate(communicationException);
		}
	}
}
{% endhighlight %}

But this will never work as the type provided in the catch clause is not at all reifiable.
