---
layout: post
title: "Array Reification I"
author: amit
modified:
excerpt: "Array Reification in details"
tags: [Java]
---

{% include _toc.html %}

## Overview

I have previously talked about **Reification** in another post. Reification signifies the run-time component representation. Arrays can reify their component types. It means array types can be represented properly in run time.

### Old School Example

{% highlight java %}
public final class ArrayReification {

	public static void main(final String[] args) {
		final Integer[] integers = new Integer[5];
		final Number[] numbers = integers;
		numbers[0] = 1.1;
	}

}
{% endhighlight %}

Any clue about the problem that the application might encounter?

It suffers from **ArrayStoreException** at run-time because the actural array object has the component type of **Integer** which can not store any value of type **Double**.

### Parameterized Array

Let's have a look at the following problem now.

{% highlight java %}
public final class Helper {

	public static <T> T[] toArray(final Collection<T> collection) {
		final T[] arr = new T[collection.size()];
		int i = 0;
		for (final T val : collection) {
			arr[i++] = val;
		}
		return arr;
	}

}
{% endhighlight %}

Is the code correct?

The aforementioned code will have a compilation error.

{% highlight java %}
Cannot create a generic array of T
{% endhighlight %}

The error occurs due to the type variable of not being a reifiable type.

Let's look at another example.

{% highlight java %}
public final class ArrayOfList {

	public static void main(final String[] args) {
		final int SIZE = 10;
		final List<Integer>[] arrayOfLists = new List<Integer>[SIZE];
	}
}
{% endhighlight %}

This program also suffers from the same compilation error.

{% highlight java %}
Cannot create a generic array of T
{% endhighlight %}

### Solution

Best probable solution is to use any collection type to contain list of data.
