---
layout: post
title: "Java 8 Optionals"
author: amit
modified:
share: true
comments: true
excerpt: "Uses and Practices"
tags: [Java, Java 8, Optional]
---

{% include _toc.html %}

## Overview

While programming, we have all faced the most (in)famous **NullPointerException**. And I believe we all would agree that encountering **NullPointerException** is also a pain. Just to keep the readers informed, the famous computer scientist Tony Hoare introduced **null** references and he thinks this as **A million-dollar mistake**. We all know, it's very easy to implement but it's quite unpredictable as well. And that's why developers need to be very cautious.

## The Usual Way

Let's consider 3 simple POJOs as follows.

```java
public class Employee {
  private Car car;

  public Car getCar() {
    return car;
  }
}

public class Car {
  private Insurance insurance;

  public Insurance getInsurance() {
    return insurance;
  }
}

public class Insurance {
  private String name;

  public String getName() {
    return name;
  }
}
```

Just to give a context - an employee can own a car (not mandatory though), a car can have insurance (not necessarily) and an insurance must have a name always. Just keep it mind to understand the folloiwng sections.

Now let's say we want to get the name of insurance by providing a person instance.

```java
public String getInsuranceName(Employee employee) {
  if (employee != null) {
    Car car = employee.getCar();
    if (car != null) {
      Insurance insurance = car.getInsurance();
      if (insurance != null) {
        return insurance.getName();
      }
    }
  }
  return "UNKNOWN";
}
```

This is what we usually do to take preventive measures so that we don't encounter dreaded **NullPointerException**. We also feel this also pollutes the source code and according to my opinion, it should be considered as an antipattern.

## Another Usual Way

Such deep nesting for null checks as mentioned in the previous section looks a bit obtruse. And sometime people do it in different way.

```java
public String getInsuranceName(Employee employee) {
  if (employee == null) {
    return "UNKNOWN";
  }
  Car car = employee.getCar();
  if (car == null) {
    return "UNKNOWN";
  }
  Insurance insurance = car.getInsurance();
  if (insurance == null) {
    return "UNKNOWN";
  }
  return insurance.getName();
}
```

This looks pretty okay to me as it doesn't comprise the deep nesting null checks. But still it follows the same antipattern to check for nulls in a bit different way.

## Why NULL is not good?

1. It deteriorates the readability of the source code
2. It is not semantically correct to present something without a value
3. It is against the ideology of Java as Java hides pointers from the developers except in the situation of null references

## Alternatives to NULL

Few languages such as Scala, Groovy removed the dreaded use of null references to signify the absence of a value. The similar code can be written in Groovy in a very concise manner.

```java
def name = employee?.car?.insurance?.name
```

**?.** this is known as **Safe Navigation operator** in Groovy and it clearly shows a much readable code while at the same time removing the possibility of encountering dreaded null references.

## Java's Endeavor

Now we should ask, what a Java developer can do to achieve the similar thing that prevents the possibility of **NullPointerException**s while maintaining a readable and maintainable source code. Java language designers chose the similar approach that Groovy or Scala language has already implemented but with the introduction of a new class - **Optional**

### Optional

```java
public final class Optional<T> {
  public static<T> Optional<T> empty() {}
  public static <T> Optional<T> of(T value) {}
  public static <T> Optional<T> ofNullable(T value) {}
  public T get() {}
  public boolean isPresent() {}
  public void ifPresent(Consumer<? super T> consumer) {}
  public Optional<T> filter(Predicate<? super T> predicate) {}
  public<U> Optional<U> map(Function<? super T, ? extends U> mapper) {}
  public<U> Optional<U> flatMap(Function<? super T, Optional<U>> mapper) {}
  public T orElse(T other) {}
  public T orElseGet(Supplier<? extends T> other) {}
  public <X extends Throwable> T orElseThrow(Supplier<? extends X> exceptionSupplier) throws X {}
}
```
This class is primarily used to signify the absence or presence of a value. If you believe a value can or cannot be present always, it is better to use Optional type. In our previous example, an employee may or may not contain a car and that's why it is better to return **Optional &lt;Car&gt;** instead of simply returning **Car**.

Let's see how we could design our previous example:

```java
public class Employee {
  private Car car;

  public Optional<Car> getCar() {
    return Optional.ofNullable(car);
  }
}

public class Car {
  private Insurance insurance;

  public Optional<Insurance> getInsurance() {
    return Optional.ofNullable(insurance);
  }
}

public class Insurance {
  private String name;

  public String getName() {
    return name;
  }
}
```

I have not discussed about the static factory **ofNullable(..)** method but just consider it as a wrapper utility method that wraps a value irrespective of its reference.

Just by seeing the API, one could easily understand what needs to be done when an optional type is encountered. For a developer, an encounter with such optional type always signifies the possibility of absence of a value and hence developer can take proper measures for this.

### Optional Creation

From the class overview, we can clearly see that an **Optional** can be created in variegated ways.

1. **of(..)**: This allows creation of **Optional** instance wrapping a non-null value
2. **empty()**: This creates an empty **Optional**
3. **ofNullable(..)**: This allows creation of an **Optional** instance wrapping any value (null or non-null)

### Optional Extraction and Transformation

So far we have already seen how to create **Optional** instances. Now we should see how to extract the value or transform it to another.

1. **get()** This returns the contained value or it throws **NoSuchElementException** if the **Optional** instance is empty

But how should we use this?

```java
Car car = employee.getCar();
if (employee != null) {
  car = employee.getCar();
}
```

This is what we mostly do to evade **NullPointerException**s. Now with Java 8 **Optional**, we can write the same as follows:

```java
Optional<Car> car = employee.getCar();
if (!car.isEmpty()) {
  Car car = car.get();
}
```

*But do you consider it as an improvement over the nasty null checks?*

I used to consider it as an improvement as it hides the null pointers but later on, I felt it pollutes source code quite a bit. But I am not against the use of returning **Optional** as types from methods or wrapping variables. I will discuss my reasons behind it in the following sections.

Let's consider the previous method:

```java
public String getInsuranceName(Employee employee) {
  return employee.getCar().getInsurance().getName();
}
```

This is a very clean code but the **NullPointerException** is lurking behind and that's why we require to incorporate several null reference checks (we have already seen this previously).

If we incorporate public String **Optional** in desiging a good API, this could have been achieved in a more concise way:

```java
public String getInsuranceName(Optional<Employee> employee) {
  return employee.flatMap(Employee::getCar)
                 .flatMap(Car::getInsurance)
                 .map(Insurance::getName)
                 .orElse("UNKNOWN");
}
```

Isn't it really nice and cleaner approach? I know this gets confusing to some programmers who are not yet comfortable with Java Streams API. I would strongly suggest to have a quick understanding on Java 8 Streams to understand the beauty of **Optional**s.

Another example would be to get insurance name if the name of the person starts with "P"

```java
public String getInsuranceName(Optional<Employee> employee) {
  return employee.filter(e-> e.getName().startsWith("P"))
                 .flatMap(Employee::getCar)
                 .flatMap(Car::getInsurance)
                 .map(Insurance::getName)
                 .orElse("UNKNOWN");
}
```

## Design Practices

Now I would like to share some ideas on designing our previously discussed POJOs in a bit different way.

### API Design Practice 1


```java
public class Employee {
  private Optional<Car> car;

  public Optional<Car> getCar() {
    return car;
  }
}

public class Car {
  private Optional<Insurance> insurance;

  public Insurance getInsurance() {
    return insurance;
  }
}

public class Insurance {
  private String name;

  public String getName() {
    return name;
  }
}
```

Here, I have declared the member variable to be of **Optional** type. According to my opinion, this is also very user-friendly and users or consumers of this class can easily understand the nature of this class. In this context, an employee **has a** car which is **Optional**, that is, an employee **may** or **may not** have a car as well.

### API Design Practice 2

```java
public class Employee {
  private Car car;

  public Optional<Car> getCar() {
    return Optional.ofNullable(car);
  }
}

public class Car {
  private Insurance insurance;

  public Optional<Insurance> getInsurance() {
    return Optional.ofNullable(insurance);
  }
}

public class Insurance {
  private String name;

  public String getName() {
    return name;
  }
}
```

This is also quite intuitive but it lacks the idea of clearly showing the absence of a member instance. To understand any system, developers always require to understand the object model first and understanding an object model requires us to understand the domain objects. In this scenario, an employee is a domain object that **has a** car as if it is mandatory for an employee. But in reality, an employee may or may not have a car. We could achieve it when we get or retrieve its value (**getCar()**) and we could then notice its possibility of the absence of contained value as the method returns **Optional**.

### What to use?

It solely depends on the developers. I personally prefer the first approach as it is clear in understading the domain model while the second approach has advantages on seralization. As **Optional** doesn't implement **Serializable**, it is not serializable in our first approach. If we use DTOs, we can adapt our implementation to the second approach.

### Optional in Method or Constructor Arguments

As I have mentioned previously that **Optional** in classes clearly shows what the consumers are supposed to do. So, if a constructor or method accepts **Optional** element as argument, it means that the argument is not mandatory.

On the other hand, we need to pay the price of polluting the codebase with **Optional**s. It is developer's sole descretion to use it carefully. I personally prefer not to use **Optional** in method arguments while if needed we can still wrap it inside **Optional** instance and perform the necessary operations on it.

### Optional in Method Return Type

Java Language Architect Brian Goetz also advices to return **Optional** in methods if there is a possiblity to return null. We have already seen this in our API Design Practice 2.

### Throw Exception from Methods or Return Optional

Since years, Java developers follow the usual way to throwing exceptions to signify an erroneous situation in a method invocation.

```java
public static InputStream getInputStream(final String path) {
        checkNotNull(path, "Path cannot be null");
        final URL url = fileSystem.getEntry(path);
        InputStream xmlStream;
        try {
            xmlStream = url.openStream();
            return xmlStream;
        } catch (final IOException ex) {
            throw new RuntimeException(ex);
        }
}
```

If the consumer of this method encounters a **RuntimeException**, that's due to the problem in opening a connection to the specified URL. On the other hand, we could also use **Optional** the following way:

```java
public static Optional<InputStream> getInputStream(final String path) {
        checkNotNull(path, "Path cannot be null");
        final URL url = fileSystem.getEntry(path);
        InputStream xmlStream;
        try {
            xmlStream = url.openStream();
            return Optional.of(xmlStream);
        } catch (final IOException ex) {
            return Optional.empty();
        }
}
```

I think this is very intuitive as it clearly says that it returns an **Optional** instance that may or may not have a value. And that's why my inclination is to return **Optional** from methods that could have such null encounter possiblity.

### Optional Return Type in Private Methods

Private methods are not clearly meant to understand or analyse any significant part of a project. And hence, I think we still can make use of null checks to get rid of too much **Optional**s but if you think you still can use the method in a more clean and concise way, you can return **Optional** as well.

For better comprehension, I have formulated an example as follows:

```java
private void process(final String data) {
        try {
            final ItemList nList = doc.getChildNodes();

            for (int temp = 0; temp < nList.getLength(); temp++) {
                final Node nNode = nList.item(temp);
                final String key = nNode.getName();
                final String value = nNode.getValue();
                values.put(getAttribute(key).orElseThrow(IllegalArgumentException::new), value);
            }
        } catch (final Exception ex) {
            logger.error("{}", ex.getMessage(), ex);
        }
}

private Optional<Attribute> getAttribute(final String key) {
        return Arrays
                      .stream(Attribute.values())
                      .filter(x -> x.value()
                                    .filter(y -> y.equalsIgnoreCase(key))
                                    .isPresent())
                      .findFirst();
}

public static enum Attribute {

    A ("Sample1"),
    B ("Sample2"),
    C ("Sample3");
    
    private String value;
    
    private Attribute(String value) {
        this.value = value;
    }
    
    public Optional<String> value() {
        return Optional.ofNullable(value);
    }

}
```

I could have written the second method in a more usual way:

```java
private Attribute getAttribute(final String key) {
        for (final Attribute attribute : Attribute.values()) {
            Optional<String> value = attribute.value();
            if (value.isPresent() && value.get().equalsIgnoreCase(key)) {
                return attribute;
            }
        }
        throw new IllegalArgumentException();
}
```

### Optional Return Type in Private Methods returning Collections or any of its subtypes

As a first example consider the code you need to implement a method to list files from a specified path in Java

```java
public static List<String> listFiles(String file) {

    List<String> files;
    try {
        files = Files.list(Paths.get(path));
    } catch (IOException e) {
        files = Arrays.asList("Could not list");
    }

    return files;
}
```

We could achieve a more concise code as follows:

```java
public static List<String> listFiles(String path) {
        
    return Files.list(Paths.get(path))
                .filter(Files::isRegularFile)
                .collect(toList());
}
```

Notice that the return type in the concise method still remains **List** instead of **Optional**. It is preferable to follow the usual practice of returning an empty list instead of using **Optional**.

It is quite patent that the stream ways of using **Optional** are more concise. **Optional** is an utility data container that helps developers to get rid of null references. In addition, it does provide lots of useful method that eases the programmer's task. But **Optional** can be misused heavily and can pollute the codebase if the developer is not well aware of the primary use of **Optional**s. That's why I strongly suggest everyone to have a go on the stream oriented methods in **Optional**s that help developers to write concise and maintainable code.