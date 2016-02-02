---
layout: post
title: "Efficient way to copy objects in Java"
author: amit
modified:
share: true
comments: true
excerpt: "The most reliable solution to copy objects in Java"
tags: [Java, Copy Constructor, Clone]
---

{% include _toc.html %}

## Overview

This is my final post on the discussion of copying objects efficiently in Java. I assume you have already gone through two of my previous articles - **Default Copy Constructor in Java**, **Cloning to copy objects in Java**. If not, it is advisable to go through those articles first to better understand this article.

### Problems

1. *Default Copy Constructor*: Clearly **new** keyword is not polymorphic which renders code inextensible.
2. *clone* method: **final** fields are not mutable using clone method

### Solution

Let's try to combine both the methodologies to achieve our goal.

### Result POJO

{% highlight java %}
public class Result implements Cloneable {

	private static int _id;
	private int id;
	private final int marks;

	{
		synchronized (this.getClass()) {
			this.id = _id++;
		}
	}

	public Result(final int marks) {
		this.marks = marks;
	}

	protected Result(final Result result) {
		checkNotNull(result);

		this.marks = result.getMarks();
	}

	@Override
	protected Object clone() throws CloneNotSupportedException {
		return new Result(this);
	}

	public int getMarks() {
		return this.marks;
	}

	@Override
	public String toString() {
		return MoreObjects.toStringHelper(this.getClass()).add("marks", this.marks).add("id", this.id).toString();
	}

}
{% endhighlight %}

You can see that the aforementioned class still has the **clone** method but the implementation is a bit changed. Now, it is using it's own **Copy Constructor** to create a whole new object.

### ExcellentResult

{% highlight java %}
public final class ExcellentResult extends Result {

	protected ExcellentResult(final ExcellentResult result) {
		super(result);
	}

	public ExcellentResult(final int marks) {
		super(marks);
	}

	@Override
	protected Object clone() throws CloneNotSupportedException {
		return new ExcellentResult(this);
	}

}
{% endhighlight %}

This derived class also contains the clone method but the implementation uses its own **Copy Constructor** to create a new object by copying its contents.

### Student POJO

{% highlight java %}
public final class Student implements Cloneable {

	private final String name;

	private final Result result;

	public Student(final String name, final Result result) {
		checkNotNull(name);
		checkNotNull(result);

		this.name = name;
		this.result = result;
	}

	protected Student(final Student student) throws CloneNotSupportedException {
		this.name = student.getName();
		this.result = (Result) student.getResult().clone();
	}

	@Override
	protected Object clone() throws CloneNotSupportedException {
		return new Student(this);
	}

	public String getName() {
		return this.name;
	}

	public Result getResult() {
		return this.result;
	}

	@Override
	public String toString() {
		return MoreObjects.toStringHelper(this.getClass()).add("name", this.name).add("result", this.result).toString();
	}

}
{% endhighlight %}

Pretty much same as we did for **Result** and its derived **ExcellentResult** class.

### Client Application

{% highlight java %}
public class MixedApproachInCloning {

	public static void main(final String[] args) throws CloneNotSupportedException {
		final Result result1 = new Result(50);
		final Student student1 = new Student("Sam", result1);
		System.out.println(student1);

		final Student student2 = (Student) student1.clone();
		System.out.println(student2);
	}

}
{% endhighlight %}

### Benefits

Now, if we execute the aforementioned client application, we can see that the current implementation is conforming to our **Deep Copy** paradigm. Even though **new** is not polymorphic, at runtime the polymorphic **clone** method invokes the actual **copy constructor** on the runtime type of **Result** object.

### Solution

Our approach should be to combine both the copy constructor and the clone method to deep copy an object in Java.
