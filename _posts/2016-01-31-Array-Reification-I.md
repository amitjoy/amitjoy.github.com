---
layout: post
title: "Array Reification I"
author: amit
modified:
share: true
comments: true
excerpt: "Array Reification in details"
tags: [Java]
---

{% include _toc.html %}

## Overview

Reification signifies the run-time component representation. It means, it represents a type which can be completely represented at runtime.

Eg, A primitive type, non-parameterized class or interface type, parameterized type of unbounded wildcard ({% highlight java %}List<?>{% endhighlight %}), raw type (List), Array whose component is reifiable ({% highlight java %}List<?>[], int[], List[]{% endhighlight %}).

Arrays can reify their component types. It means array types can be represented properly in runtime.

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

*Any clue about the problem that the application might encounter?*

It suffers from **ArrayStoreException** at runtime because the actual array object has the component type of **Integer** which can not store any value of type **Double**.

### Parameterized Array

Let's have a look at the following snippet to transform a collection to an array..

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

*Is the code correct?*

No, it's not. It will result in a compilation error.

{% highlight java %}
Cannot create a generic array of T
{% endhighlight %}

The error occurs due to the type variable of not being a reifiable type. <br />

Let's look at another example.

{% highlight java %}
public final class ArrayOfList {

	public static void main(final String[] args) {
		final List<String> cars = asList("Audi", "BMW", "Kia", "Tata");
		final List<String> universities = asList("TU Munich", "RWTH Aachen", "Uni Stuttgart", "TU Kiel");
		final List<String>[] arrayOfLists = new List<String>[] { cars, universities };
	}
}
{% endhighlight %}

This program also suffers from the same compilation error.

{% highlight java %}
Cannot create a generic array of List<String>
{% endhighlight %}

### Solution

So, can't we use generic arrays for our purpose? No, we can use generic arrays but it is advisable to use any collection type to contain list of data which comprises more operations to be performed on the data container.

I would love to discuss more on **Array Reification** in future posts to cover some of the important aspects.
