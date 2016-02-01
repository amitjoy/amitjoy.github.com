---
layout: post
title: "Array Reification II"
author: amit
modified:
share: true
comments: true
excerpt: "Array Reification in details"
tags: [Java]
---

{% include _toc.html %}

## Overview
This post is a continuation of the previous post on **Array Reification**. Today, I am going to talk about how to convert a collection to array efficiently.

### Example Conversion

{% highlight java %}
public final class Helper {

  	public static void main(final String[] args) {
  		final List<String> strings = asList("a", "b", "c");
  		final String[] array = toArray(strings);
  		System.out.println(array);
  	}

  	public static <T> T[] toArray(final Collection<T> collection) {
  		final T[] arr = (T[]) new Object[collection.size()];
  		int i = 0;
  		for (final T val : collection) {
  			arr[i++] = val;
  		}
  		return arr;
  	}
}
{% endhighlight %}

If you run the afore-mentioned example in Java 1.8.0_71 (as it is the currently installed Java JDK in my local machine), you would encounter a **ClassCastException** in run-time.

Along with it, you would also encounter an **Unchecked Cast** warning as follows.

{% highlight java %}
Type safety: Unchecked cast from Object[] to T[]
{% endhighlight %}

If you look closely to the exception that it encounters,

{% highlight java %}
Exception in thread "main" java.lang.ClassCastException: [Ljava.lang.Object; cannot be cast to [Ljava.lang.String; at com.amitinside.practice.Helper.main(Helper.java:13)
{% endhighlight %}

it clearly shows that Object array can not be casted to String array because the actual type of the array is an Object array and in run-time to cast to String array, causes the exception. Even though the actual array provided in main method is a String array, while assigning in main method the converted array to a String Array fails because the actual reified type is an Object array. The actual reified type should have been String array.

### Principle of Truth in Advertising

The reified type of an array must be a subtype of the erasure

So, according to this, in the previous problem, T has been bound to String but the reified type of the array is still Object.

### Cast-Iron Guarantee

Casts inserted automatically by erasure will never fail unless your application code has **Unchecked Warning**.

This clearly states that due to erasure, the casts will never fail in run-time unless your code has the **Unchecked Warning** and in our scenario, we do have an **Unchecked Warning** which causes the **ClassCastException** in run-time.
