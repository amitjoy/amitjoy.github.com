---
layout: post
title: "Cloning to copy objects in Java"
author: amit
modified:
share: true
comments: true
excerpt: "The temporary solution to copy objects in Java"
tags: [Java, Copy Constructor]
---

{% include _toc.html %}

## Overview

This post is a continuation to my previous article on **Default Copy Constructor in Java**. If you haven't read through it, I would suggest you to read that article first before going through this one.

Today I am going to discuss about the temporary solution to copying objects in Java using **clone** method in **Object** class.

### Recap on the Problem

The problem we have faced regarding our previous solution is maintainability.

1. Let's say we have a requirement to add another new property to the **Result** class, namely **Grade**. This procedure of adding **Copy Constructor** to each and every class has to be done.

2. Let's consider another example. If we have several different kinds of **Result** types then in **Student** copy constructor we have to have an **instanceof** test on each and every type of **Result** instance which eventually makes the code less readable, error-prone and not-extensible.

The previous solution also violates **Open-Closed Principle** and hence it is not advisable.

### Snippet

Let's start with some more concrete types of **Result*s.

### Result POJO

{% highlight java %}
public class Result {

	private final int marks;

	public Result(final int marks) {
		this.marks = marks;
	}

	public Result(final Result result) {
		this.marks = result.getMarks();
	}

	public int getMarks() {
		return this.marks;
	}

	@Override
	public String toString() {
		return MoreObjects.toStringHelper(this.getClass()).add("marks", this.marks).toString();
	}

}
{% endhighlight %}

### ExcellentResult

{% highlight java %}
public final class ExcellentResult extends Result {

	public ExcellentResult(final ExcellentResult result) {
		super(result.getMarks());
	}

	public ExcellentResult(final int marks) {
		super(marks);
	}

}
{% endhighlight %}

### Student POJO

{% highlight java %}
public final class Student {

	private final String name;

	private final Result result;

	public Student(final String name, final Result result) {
		checkNotNull(name);
		checkNotNull(result);

		this.name = name;
		this.result = result;
	}

	public Student(final Student student) {
		this.name = student.getName();
		Result _result = student.getResult();
		if (_result instanceof ExcellentResult) {
			this.result = new ExcellentResult((ExcellentResult) _result);
		} else {
			this.result = new Result(_result);
		}
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

Here, in this class, we have to add all the different **instanceof** test to check for actual **Result** instance before delegating it to the copy constructor method.

Unless, we will end up confronting the same problem of **Shallow Copy**.

## Solution - Clone

In the **Object** class, we have a method **clone** which we can make use of in case of copying objects.

### Result POJO

{% highlight java %}
public class Result implements Cloneable {

	private int marks;

	public Result(final int marks) {
		this.marks = marks;
	}

	@Override
	protected Object clone() throws CloneNotSupportedException {
		final Result result = (Result) super.clone();
		result.marks = this.marks;
		return result;
	}

	public int getMarks() {
		return this.marks;
	}

	@Override
	public String toString() {
		return MoreObjects.toStringHelper(this.getClass()).add("marks", this.marks).toString();
	}

}
{% endhighlight %}

In the aforementioned class, we have inherited the **clone** method and implemented it to return a new **Result** instance by copying the properties.

### Student POJO

{% highlight java %}
public final class Student implements Cloneable {

	private String name;

	private Result result;

	public Student(final String name, final Result result) {
		checkNotNull(name);
		checkNotNull(result);

		this.name = name;
		this.result = result;
	}

	@Override
	protected Object clone() throws CloneNotSupportedException {
		final Student student = (Student) super.clone();
		student.name = this.name;
		student.result = (Result) this.result.clone();
		return student;
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

In **Student** class, we have inherited the **clone** method and implemented it accordingly by copying the properties. But you can see that I have passed a newly cloned **Result** object to the constructor argument. Due to this, we achieve **Deep Copying** so that we make sure each and every newly cloned **Student** object will contain a newly cloned **Result** instance.

### The differences

Now you might be wondering about the subtle differences in these two solutions - one with **clone** method and one with **Default Copy Constructor**.

The most noticeable difference is that to make use of **clone** method, our **Student** and **Result** POJO extended **Cloneable** interface.

Now let's talk about the actual differences in using **clone** method.

Let's consider, we have a subtype of **Result** class as follows.

{% highlight java %}
public final class ExcellentResult extends Result {

	public ExcellentResult(final int marks) {
		super(marks);
	}

	@Override
	protected Object clone() throws CloneNotSupportedException {
		return super.clone();
	}

}
{% endhighlight %}

According to the definition of **clone** method in **Result** class, it will automatically handle the runtime type of any **Result** type and it will invoke the respective **clone** method from any subtype of **Result** class. This occurs because methods are polymorphic in nature.

In the previous solution, we introduced **Default Copy Constructor** which used to create respective objects using **new** keyword. In Java, **new** is not at all polymorphic in nature and that's why we had to introduce the **instanceof** type check to verify the runtime type of any specific **Result** class.

### Client Application

{% highlight java %}
public final class CloneMethod {

	public static void main(final String[] args) throws CloneNotSupportedException {
		final Result result1 = new Result(50);
		final Student student1 = new Student("Sam", result1);

		final Student student2 = (Student) student1.clone();
		System.out.println(student2);
	}

}
{% endhighlight %}

Now if we use **clone** the aforementioned way, we would be able to deep copy objects.

### Problem

*Do we really have any problem in using clone method?*

We can deep copy objects, it's polymorphic as well. Then what is the problem?

Let's now change our **Result** POJO a bit.

### Result POJO - Modified

{% highlight java %}
public class Result implements Cloneable {

	private static int _id;
	private int id;
	private int marks;

	{
		synchronized (this.getClass()) {
			this.id = _id++;
		}
	}

	public Result(final int marks) {
		this.marks = marks;
	}

	@Override
	protected Object clone() throws CloneNotSupportedException {
		final Result result = (Result) super.clone();
		result.marks = this.marks;
		result.id = _id++;
		return result;
	}

	public int getMarks() {
		return this.marks;
	}

	@Override
	public String toString() {
		return MoreObjects.toStringHelper(this.getClass()).add("marks", this.marks).toString();
	}

}
{% endhighlight %}

The aforementioned code has been slightly modified by introducing identifier property to the **Result** class. The **clone** method has been modified according to cope up with the recent changes.

### Problem

Now, if we make our identifier property constant, then the clone method will report a compilation error as it wouldn't be able to reassign a value to a constant.

{% highlight java %}
public class Result implements Cloneable {

	private static int _id;
	private final int id;
	private int marks;

	{
		synchronized (this.getClass()) {
			this.id = _id++;
		}
	}

	public Result(final int marks) {
		this.marks = marks;
	}

	@Override
	protected Object clone() throws CloneNotSupportedException {
		final Result result = (Result) super.clone();
		result.marks = this.marks;
		result.id = _id++;
		return result;
	}

	public int getMarks() {
		return this.marks;
	}

	@Override
	public String toString() {
		return MoreObjects.toStringHelper(this.getClass()).add("marks", this.marks).toString();
	}

}
{% endhighlight %}

The following assignment in the **clone** method will not work in case we change the **id** to a constant.

{% highlight java %}
result.id = _id++;
{% endhighlight %}

### Solution

We are facing problems with the **Default Copy Constructor** as well as **clone** method. So, what would come to rescue us?

I think we can have a look at the solution in my soon-to-come post.
