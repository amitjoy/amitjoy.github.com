---
layout: post
title: "Convert a Collection to an Array"
author: amit
modified:
share: true
comments: true
excerpt: "Different ways to transform a collection to an array"
tags: [Java, Generics]
---

{% include _toc.html %}

## Overview

This post is a continuation to the previous posts on **Array Reification**. Today I would like to discuss about different ways to convert a collection to an array.

### Method 1

{% highlight java %}
public final class ConvertCollectionToArray {

	public static void main(final String[] args) {
		final List<String> names = Lists.newArrayList("Sam", "Dean", "Lucifer");
		final String[] namesArray = new String[3];
		System.out.println(names.toArray(namesArray));
	}

}
{% endhighlight %}

The collections framework contains two utilitarian methods.

{% highlight java %}
public object[] toArray();
public <T> T[] toArray(T[] a);
{% endhighlight %}

The first method converts the concrete collection instance to an Object array whereas the second one converts the collection instance to the reified type (runtime type) of the array instance provided as a parameter to it.

### Method 2

{% highlight java %}
public final class ConvertCollectionToArray {

	public static void main(final String[] args) {
		final List<String> names = Lists.newArrayList("Sam", "Dean", "Lucifer");
		final String[] namesArray = new String[3];
		System.out.println(toArray(names, namesArray));
	}

	public static <T> T[] toArray(final Collection<T> collection, T[] array) {
		if (array.length < collection.size()) {
			array = (T[]) Array.newInstance(collection.getClass().getComponentType(), collection.size());
		}

		int i = 0;
		for (final T a : collection) {
			array[i++] = a;
		}

		if (i < array.length) {
			array[i] = null;
		}
		return array;
	}

}
{% endhighlight %}

In this example, we tried to mimic the actual behavior in our own utility method. In this method, we first check whether the length of the array provided as argument is smaller than the size of the actual collection instance. If so, we use **reflection** to create another instance of the reified type of the array with the same size of the collection.

Finally, we just need to insert the elements of the collection to the array.

### Method 3

{% highlight java %}
public final class ConvertCollectionToArray {

	public static void main(final String[] args) {
		final List<String> names = Lists.newArrayList("Sam", "Dean", "Lucifer");
		System.out.println(toArray(names, String.class));
	}

	public static <T> T[] toArray(final Collection<T> collection, final Class<T> clazz) {
		final T[] array = (T[]) Array.newInstance(clazz.getComponentType(), collection.size());

		int i = 0;
		for (final T a : collection) {
			array[i++] = a;
		}
		return array;
	}

}
{% endhighlight %}

This example is similar to the previous one but the difference lies in the array creation. In the previous example, the user needs to provide an array instance to the method but in this method, we need user to provide the reified type of the actual array. I basically prefer this example as this example is less noisy and more user-friendly. If you are not aware of the **Class&lt;T&gt;** syntax, it is a representation of a class at runtime. In Java 5, this class **Class** has been rendered generic **Class&lt;T&gt;**.

As an example, **String.class** has a type of **Class&lt;String&gt;**.

### Unchecked Cast Warning

If you have tried running the aforementioned custom examples, you would encounter **Unchecked Cast Warning** message which occurs due to the cast from a non-qualified type to a generic type.
