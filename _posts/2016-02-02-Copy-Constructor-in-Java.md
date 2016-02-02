---
layout: post
title: "Default Copy Constructor in Java"
author: amit
modified:
share: true
comments: true
excerpt: "The viability of Default Copy Constructor in Java"
tags: [Java, Copy Constructor]
---

{% include _toc.html %}

## Overview

In the good old days of our programming, we all have learnt about C++. If you remember, in C++, we used to have **Copy Constructor** to copy an object. But in Java, how do we copy an object? We all are aware of the **clone** method inside **Object** class which is used to clone an object. But we still do have some fallacies in it.

### Default Copy Constructor

Let's consider we have an entity called **Student** which holds another entity called **Result**.

### Result POJO

{% highlight java %}
public final class Result {

	private final int marks;

	public Result(final int marks) {
		super();
		this.marks = marks;
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

### Student POJO

{% highlight java %}
public final class Student {

	private final String name;

	private final Result result;

	public Student(final String name, final Result result) {
		super();
		this.name = name;
		this.result = result;
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

So, each and every object of **Student** will hold a pointer to the actual **Result** object.

### Application Client

{% highlight java %}
public final class DefalutCopyConstructor {

	public static void main(final String[] args) {
		final Result result = new Result(50);
		final Student student = new Student("Sam", result);
		System.out.println(student);
	}

}
{% endhighlight %}

If you run the previous code, you can get a **Student** object which holds a reference to the **Result** object. Now, if I want to create another object, I can create it the same way I did in the previous example.

{% highlight java %}
public final class DefalutCopyConstructor {

	public static void main(final String[] args) {
		final Result result1 = new Result(50);
		final Student student1 = new Student("Sam", result1);

		final Result result2 = new Result(50);
		final Student student2 = new Student("Dean", result2);

		System.out.println(student1);
		System.out.println(student2);
	}

}
{% endhighlight %}

Now, we have another student object created. If you check using the equality operator, it would report **false** as both the references (student1 and student2) are pointing to different objects in heap.

If we want to copy the object of student1 to a reference of student2, I would try to write a syntax like the following one.

{% highlight java %}
public final class DefalutCopyConstructor {

	public static void main(final String[] args) {
		final Result result1 = new Result(50);
		final Student student1 = new Student("Sam", result1);

		final Student student2 = new Student(student1);

		System.out.println(student1);
		System.out.println(student2);
	}

}
{% endhighlight %}

This clearly will report a compilation error.

{% highlight java %}
The constructor Student(Student) is undefined
{% endhighlight %}

But think, Java could have given us a **Default Copy Constructor** like this but it didn't.

*Why?* Because of the perils in **Shallow Copy**

### Shallow Copy

If you write the same program in C++ and try to use the default constructor to copy the object of student1 to student2, it would copy the complete object of student1. But it won't copy the actual object of result which the object of student1 holds. It will only have a pointer to the previous object (result).


student1 ------------------> result <------------------- student2

Here, student1 refers to a **Student** object which contains a **Result** object reference. Now, student2 will refer to a completely new **Student** object but this new **Student** object will still contain the **Result** reference to the old **Result** object. So, you can clearly see that the objects are not deeply copied but the references.

Due to this peril in copying object, Java didn't provide the **Default Copy Constructor**.

## Solution (Deep Copy)

We could then try to implement our own copy constructor to copy the objects deeply. Let's try to implement it.

### Result POJO with Copy Constructor

{% highlight java %}
public final class Result {

	private final int marks;

	public Result(final int marks) {
		super();
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

### Student POJO with Copy Constructor

{% highlight java %}
public final class Student {

	private final String name;

	private final Result result;

	public Student(final String name, final Result result) {
		super();
		this.name = name;
		this.result = result;
	}

	public Student(final Student student) {
		this.name = student.getName();
		this.result = new Result(student.getResult());
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

### Client Application

{% highlight java %}
public final class DefalutCopyConstructor {

	public static void main(final String[] args) {
		final Result result1 = new Result(50);
		final Student student1 = new Student("Sam", result1);

		final Student student2 = new Student(student1);

		System.out.println(student1);
		System.out.println(student2);
	}

}
{% endhighlight %}

### Benefit

Due to this implementation, we have completely got rid off the **Shallow Copy** problem. It means now, we can make exact copies of each and every object whether it is **Student** object or a **Result** object. The copy of the **Student** object will no longer refer to the **Result** object contained by any other **Student** object. Instead, it will contain a new **Result** object reference.

### Problem

But the problem lies in maintainability.

Let's say we have a requirement to add another new property to the **Result** class, namely **Grade**. This procedure of adding **Copy Constructor** to each and every class has to be done.

Let's consider another example. If we have several different kinds of **Result** types then in **Student** copy constructor we have to have an **instanceof** test on each and every type of **Result** instance which eventually makes the code less readable and error-prone.

### Solution

You might be thinking that **clone** method from **Object** class could be our key to rescue but it also incurs the similar problem. Just to keep it simple and silly, I am going to discuss about the **clone** method in my upcoming post.
