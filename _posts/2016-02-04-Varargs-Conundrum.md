---
layout: post
title: "The Varargs Conundrum"
author: amit
modified:
share: true
comments: true
excerpt: "The quirks in Variable Arguments in Java"
tags: [Java, Generics]
---

{% include _toc.html %}

## Overview

At some point, we all have used variable arguments using Java. Sometimes we find it very useful but to be frank, it is not as friendly as you think. It might incur several hassles during application development.

### Generic Array Creation

{% highlight java %}
public final class Varargs {

	public static void main(final String[] args) {
		final List<Integer> list1 = Arrays.asList(10, 20, 30);
		final List<Integer> list2 = Arrays.asList(50, 60, 70);
		final List<List<Integer>> combined = Arrays.asList(list1, list2);
		System.out.println(combined);
	}

}
{% endhighlight %}

While writing programs, we, developers are inclined to utilize such helper methods (**asList(E...)**) quite often. Basically the arguments as passed to the **asList(E...)** are stored in an array.

Now let's look at the equivalent snippet.

### Generic Array Creation - Equivalence

{% highlight java %}
public final class Varargs2 {

	public static void main(final String[] args) {
		final List<Integer> list1 = Arrays.asList(new Integer[] { 10, 20, 30 });
		final List<Integer> list2 = Arrays.asList(new Integer[] { 50, 60, 70 });
		final List<List<Integer>> combined = Arrays.asList(new List<Integer>[] { list1, list2 });
		System.out.println(combined);
	}

}
{% endhighlight %}

*Will this program compile?*

No, it won't. If you have gone through my posts on **Array Reification**, then you might remember that **Generic Array Creation** on non-refiable component type is not possible.

*So how do you make the snippet work?*

{% highlight java %}
public final class Varargs2 {

	public static void main(final String[] args) {
		final List<Integer> list1 = Arrays.asList(new Integer[] { 10, 20, 30 });
		final List<Integer> list2 = Arrays.asList(new Integer[] { 50, 60, 70 });
		final List<List<Integer>> combined = Arrays.asList(new List[] { list1, list2 });
		System.out.println(combined);
	}

}
{% endhighlight %}

It's simple, right? Just use raw type of **List** while array creation. But still, you would encounter the well-known **Unchecked Cast Warning** message.

Let's try to change the program a bit to see some interesting facts.

### Snippet 1

{% highlight java %}
public final class Varargs3 {

	public static void main(final String[] args) {
		final List<Integer> list1 = Arrays.asList(new Integer[] { 10, 20, 30 });
		final List<Double> list2 = Arrays.asList(new Double[] { 50.0, 60.0, 70.0 });
		final List<List<Integer>> combined = Arrays.asList(list1, list2);
		System.out.println(combined);
	}

}
{% endhighlight %}

*What about this?*

This will still report a compilation error. Due to the generic type check before the print statement, it will report the following error.

{% highlight java %}
Type mismatch: cannot convert from List<List<? extends Number&Comparable<?>>> to List<List<Integer>>
 {% endhighlight %}

### Snippet 2

{% highlight java %}
public final class Varargs3 {

	public static void main(final String[] args) {
		final List<Integer> list1 = Arrays.asList(new Integer[] { 10, 20, 30 });
		final List<Double> list2 = Arrays.asList(new Double[] { 50.0, 60.0, 70.0 });
		final List<List<Integer>> combined = Arrays.asList(new List[] {list1, list2});
		System.out.println(combined);
	}

}
{% endhighlight %}

Now, we have written an equivalence snippet in which we used raw **List** array. Due to this, we can see that the **List** array would accept the list of doubles. We can clearly see that the aforementioned snippet bypassed the generic type check.

### Conclusion

In general, the use of **varargs** would impose more danger than the comfort it can offer. It's always preferable to use **ArrayList** in lieu of **varargs**.
