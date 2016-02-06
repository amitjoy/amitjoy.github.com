---
layout: post
title: "Java Money"
author: amit
modified:
share: true
comments: true
excerpt: "Dealing with Monetary Calculations in Java"
tags: [Java, Java Money]
---

{% include _toc.html %}

## Overview

If you ever tried to build any application which deals in monetary computations, you might have written a responsible class dealing with such hefty operations using **BigDecimal**.

### Float/Double Arithmetic

If you try to develop the your monetary computation class using **float** or **double** arithmetic yo would encounter unexpected results which would make you believe that the operations are broken.

{% highlight java %}
public final class FloatingPointProblem {

	public static void main(final String[] args) {
		System.out.println(0.51 - .4);
	}

}
{% endhighlight %}

If you execute the aforementioned snippet, it will get the an absurd result - **0.10999999999999999**

Basically in Java, we can not represent the actual base 10 numbers and that's why such problems occur.

### Old School Solution

To overcome such challenges, the **BigDecimal** class has been introduced which proved its convenience over the floating point arithmetic.

*What's the problem?*

The problem lies in writing too much boiler-plate code which makes it hard to manage.

### JSR 354

This is a **Java Specification Request** to add new features relating to **Monetary** computations.

### Reading Material

I have found an extensive guide written by the actual developer. It covers all the nitty gritty details of Java Money API.

<a markdown="0" href="https://otaviojava.gitbooks.io/money-api/" class="btn" target="_blank">Java Money</a>
