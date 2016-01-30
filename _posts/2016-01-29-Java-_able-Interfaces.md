---
layout: post
title: "Java -able Interfaces"
author: billy_rick
modified:
excerpt: "The enumeration of Java interfaces that ends with -able"
tags: [Java]
---

{% include _toc.html %}

## Overview

For a Java Developer, it is really important to know the huge array of interfaces which ends with -able in its names. So, why do we have interfaces with such a name? Is it really difficult to answer? I don't think so. We all know about interfaces like Serializable which assists a class object to be serialized. Apart from this, we still do have several interfaces which have such a naming convention.

### Serializable

The class implementing **java.io.Serializable** interface enables its objects to be serialized. This interface doesn't have any method. Such an interface is called **Marker Interface**.

If the classes need to handle the serialization and deserialization in a different way, the class implementing **java.io.Serializable** must implement special method.

{% highlight java %}
private void writeObject(java.io.ObjectOutputStream out) throws IOException

private void readObject(java.io.ObjectInputStream in) throws IOException, ClassNotFoundException;

private void readObjectNoData() throws ObjectStreamException;
{% endhighlight %}

### Cloneable

The class that implements **java.lang.Cloneable** assures that it conforms to the contract of **Object.clone()** method to copy the instances of the class.

### Readable

If a class implements **java.lang.Readable** assures that it provides source of characters to read from. The user can read the source of characters using its **read()** method.

### Appendable

This **java.lang.Appendable** interface specifies the contract to append character values to the implementing class object. As an example, the widely used **java.lang.StringBuffer** and **java.lang.StringBuilder** implement this interface.

### Closeable

**java.io.Closeable** interface provides the contract for the user to close a source or a destination of data. It declares its contract through **close()** method which is invoked to release the currently acquired resources.

### AutoCloseable

**java.io.AutoCloseable** has been introduces lately in Java 1.7 which ensures the source or destination of data implementing **java.io.AutoCloseable** interface can be used in accordance with try with resources block. The benefit of using try with resources block is that it automatically closes the data source and it releases the current resources.

### Observable

This class ensures an object to be monitored in the Model-View Controller programming paradigm.

### Iterable

Implementing this interface allows an object to be the target of the **foreach** statement.

### Comparable

By implementing this interface, the class has the opportunity to compare the object with another specified object conforming to the order.

### Runnable

This interface is mainly used to facilitate users to provide Thread Task definition.

### Repeatable

This is basically an annotation which has been introduced in Java 8. This annotation ensures to use it multiple times as mentioned below.

{% highlight java %}
public final class Sample {

	@Repeatable(value = TechnicalUniversities.class)
	public @interface Place {
		String value();

	}

	@Retention(RetentionPolicy.RUNTIME)
	public @interface TechnicalUniversities {
		Place[] value() default {};
	}

	@Place("Munich")
	@Place("Berlin")
	@Place("Freiburg")
	@Place("Stuttgart")
	public interface TechnicalUniversity {

	};
}
{% endhighlight %}

Prior to Java 8, we could achieve the same by annotating TechnicalUniversity interface with an array of Places which looks a bit cluttered.

### Synchronizable

In a DZone Java entry by Lucas Eder, it has been mentioned that it could have been in the JDK if Java has been developed today.

Just to give you a glimpse of it, in Java we hardly use the **Synchronized** modifier

{% highlight java %}
public synchronized void func1() {}
public void func2() {
    synchronized (this) {
      //logic
    }
}
{% endhighlight %}

This modifier is unnecessary at the method level as we need to synchronize a block of code. Hence, we mostly write the following way.

{% highlight java %}
public final class Sample {

	private final Object LOCK = new Object();

	private void func() {
		synchronized (this.LOCK) {
			// actual logic
		}
	}
}
{% endhighlight %}

So, if we try to rewrite the same using the following version of code, it would work.

{% highlight java %}
public final class Sample {

	private void func() {
		synchronized ("LOCK") {
			// actual logic
		}
	}

}
{% endhighlight %}

What is the **problem**?

The problem lies in allowing each and every object to be a monitor. Instead, Java could have provided a **Synchronizable** interface to allow specific objects which can only be used as monitors.

### Threadable

We all have seen that all the lower level threading API methods are in **java.lang.Object** class. But I believe it's a bad design decision. Instead, we could have had the threading related method in a specific class called **Threadable**. This would then conform to the **Single Responsibility Principle**.
