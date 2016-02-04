---
layout: post
title: "Principle of Indecent Exposure"
author: amit
modified:
share: true
comments: true
excerpt: "One of the most important principles in Array Reification"
tags: [Java, Generics]
---

{% include _toc.html %}

## Overview

I have previously discussed about different dimensions in **Array Reification**. Today, I am going to discuss about another important phenomena in **Array Reification**

### Problem Domain

Let's consider, we have a requirement to store several lists of results in an array container. We can write our program the following way.

{% highlight java %}
public final class IndecentExposure {

	public static List<Integer>[] studentMarksLists() {
		final List<Integer>[] studentMarksLists = (List<Integer>[]) new List[2];

		final List<Integer> samMarks = Lists.newArrayList(40, 30, 48, 22, 98);
		final List<Integer> deanMarks = Lists.newArrayList(73, 98, 22, 73, 45);

		studentMarksLists[0] = samMarks;
		studentMarksLists[1] = deanMarks;

		return studentMarksLists;
	}

}
{% endhighlight %}

If you execute the program, you would encounter the famous **Unchecked Cast** warning message.

Now let's try to retrieve Sam's marks or Dean's Marks.

{% highlight java %}
public final class IndecentExposure {

	public static void main(final String[] args) {
		final List<Integer>[] studentMarksLists = IndecentExposure.studentMarksLists();
		final List<? extends Number>[] marksLists = studentMarksLists;
		marksLists[0] = Lists.newArrayList(20.23, 40.44, 50.44);
		final int num = studentMarksLists[0].get(0);
		System.out.println(num);
	}

	public static List<Integer>[] studentMarksLists() {
		final List<Integer>[] studentMarksLists = (List<Integer>[]) new List[2];

		final List<Integer> samMarks = Lists.newArrayList(40, 30, 48, 22, 98);
		final List<Integer> deanMarks = Lists.newArrayList(73, 98, 22, 73, 45);

		studentMarksLists[0] = samMarks;
		studentMarksLists[1] = deanMarks;

		return studentMarksLists;
	}

}
{% endhighlight %}

We have previously seen that generic array creation is not possible and that's the reason we needed to use the raw type **List** to create the container and then we casted it to the required type.

Now, if we try to retrieve the value from the container, we would encounter **Class cast exception** as the aforementioned code replaces the first element of the array with a list of doubles.

You can see that here we have an array whose component type is **List&lt;Integer&gt;**. As the component type is not reifiable, the array accepted a **List&lt;? extends Number&gt;**. For which, the first element of the array got replaced by a list of doubles at runtime.

### Principle of Indecent Exposure

*Do not expose any array publicly whose compile-time component type in not reifiable*

### Principle of Truth in Advertising and Principle of Indecent Exposure

In my earlier post, I have discussed about **Principle of Truth in Advertising**. Both of these principles are quite similar.

**Principle of Truth in Advertising** tells us that the runtime type of any array should be reifiable
whereas **Principle of Indecent Exposure** tells us that the compile-time component type of any array should be reifiable.
