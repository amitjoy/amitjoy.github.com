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

This post is a continuation of the previous post on **Array Reification**. Today, I am going to discuss about some important principles on **Array Reification**.

### Example

Let's have a look at the following example code to convert a collection to an array.

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

If you run the aforementioned example in Java 1.8.0_71 (as it is the currently installed Java JDK in my local machine), you would encounter a **ClassCastException** at runtime.

Along with it, you would also encounter an **Unchecked Cast** warning as follows.

{% highlight java %}
Type safety: Unchecked cast from Object[] to T[]
{% endhighlight %}

If you look closely to the exception that it encounters,

{% highlight java %}
Exception in thread "main" java.lang.ClassCastException: [Ljava.lang.Object; cannot be cast to [Ljava.lang.String; at com.amitinside.practice.Helper.main(Helper.java:13)
{% endhighlight %}

it clearly states that **Object array** can not be casted to **String array** because the actual type of the array is an **Object array** and in runtime while casting it to **String array**, causes the exception. Even though the actual array provided in main method is a **String array**, while assigning the converted array to a **String Array** fails because the actual reified type is an **Object array**. The actual reified type should have been **String array**.

### Principle of Truth in Advertising

*The reified type of an array must be a subtype of the erasure*

So, according to this principle, in the previous problem, **T** has been bound to **String** but the reified type of the array is still **Object** and hence the exception occurs.

### Cast-Iron Guarantee

*Casts inserted automatically by erasure will never fail unless your application code has Unchecked Cast Warning message*

This clearly states that due to erasure, the casts will never fail in runtime unless your code has the **Unchecked Cast Warning** messages and in our example, we do have an **Unchecked Warning** which causes the **ClassCastException** in runtime.

### Unchecked Cast Warning

Just as a reference to what **Unchecked Cast Warning** is. It is a compiler warning message due to any cast from a non-qualified type to a generic type or vice-versa.
