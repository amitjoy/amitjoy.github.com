---
layout: post
title: "All about Finals"
author: amit
modified:
share: true
comments: true
excerpt: "Uses and Practices"
tags: [Java, Final, Optimization, Readability, Maintainablity]
---

{% include _toc.html %}

## Overview

> A variable can be declared final. A final variable may only be assigned to once. It is a compile-time error if a final variable is assigned to unless it is definitely unassigned immediately prior to the assignment.

After the assignment of any final variable, it can never be altered. If the variable refers to any instance of any object, it will continue to refer to the same object instance however the internal state of the object can be modified.

## Effectively Final

A variable can also be considered as effectively final if and only if

1. it is not delcared as final
2. the assignement occurs once

I find the simplest way to explain **effectively final** is to imagine adding the final modifier to a variable declaration. If, with this change, the program continues to behave in the same way, both at compile time and at run time, then that variable is effectively final.

## Use in Lamda Expression

> Any local variable, formal parameter, or exception parameter used but not declared in a lambda expression must either be declared final or be effectively final, or a compile-time error occurs where the use is attempted.

The restriction of local variable capture to effectively immutable variables is intended to direct developers’ attention to more easily parallelizable, naturally thread-safe techniques. Mutable fields are always a potential source of concurrency problems if not properly managed. Disallowing field capture by lambda expressions would reduce the usefulness without doing anything.

## Use of Final Keyword

According to my opinion, it is a better practice to mark local variables, method arguments, field variables as final whenever appropriate. It warns other developers to not accidentally modify a value. Apart from this, it signals the compiler to optimize the class file. There are lots of arguments against this statement saying that it is not true that use of final keyword in source file enables optimization of the class file. I strongly advice to have a look at **Hardcore Java** written by Robert Simons Jr in which the use of final keyword and its optimizations have been detailed.

I feel the application of final keywords makes the intent of the code clear to any developer. Making a variable final relieves the programmer of excess stress to scan through the code to see if the variable has changed anywhere after its initialization.  It is also much safer than non-final variables in terms of maintaining states in multithreaded environment.

According to the detailed benchmark as provided by [RustyX](https://stackoverflow.com/users/485343/rustyx) in a StackOverflow [thread](), the following program demonstrates a noticeable performance gain:

```java
public class FinalTest {

    public static final int N_ITERATIONS = 10_00_000;

    public static String testFinal() {
        final String a = "a";
        final String b = "b";
        return a + b;
    }

    public static String testNonFinal() {
        String a = "a";
        String b = "b";
        return a + b;
    }

    public static void main(String[] args) {
        long tStart, tElapsed;

        tStart = System.currentTimeMillis();
        for (int i = 0; i < N_ITERATIONS; i++)
            testFinal();
        tElapsed = System.currentTimeMillis() - tStart;
        System.out.println("Method with finals took " + tElapsed + " ms");

        tStart = System.currentTimeMillis();
        for (int i = 0; i < N_ITERATIONS; i++)
            testNonFinal();
        tElapsed = System.currentTimeMillis() - tStart;
        System.out.println("Method without finals took " + tElapsed + " ms");

    }

}
```

the result was quite outstanding:

```java
Method with finals took 5 ms
Method without finals took 273 ms
```

I also personally prefer to lean towards the use of immutable classes and use of final keyword as instance variables signifies this. According to Joshua Bloch in **Effective Java**,

> Immutable classes are easier to design, implement, and use than mutable classes. They are less prone to error and are more secure.. Furthermore An immutable object can be in exactly one state, the state in which it was created. vs Mutable objects, on the other hand, can have arbitrarily complex state spaces.

### Static Code Anaylsis Tools

Static Analysis tool like PMD is one of the biggest proponents of the usage of final keyword whenever appropriate. For more information, have a look at [this](http://pmd.sourceforge.net/pmd-4.3.0/rules/optimizations.html). FindBugs and CheckStyle is configured to warn on variables if final keyword is missing. I have found this [link](http://checkstyle.sourceforge.net/config_misc.html#FinalParameters) that supports the use of final keywords in Checkstyle:

> Changing the value of parameters during the execution of the method's algorithm can be confusing and should be avoided. A great way to let the Java compiler prevent this coding style is to declare parameters final. 

### Eclipse Save Action

Eclipse IDE provides useful **Save Actions Preferences page** that lets you apply changes primarily focused on optimizing source code. One of the **Save Actions** is the application of final keywords to variables whenever appropriate.

## Conclusion

In terms of clear desing and readability, I support the use of final keyword in source files whereas few of my fellow Java developers would disagree with me due to the visual clutter that it introduces. I agree that seemingly the use of final keywords in local variables, instance variables, method arguments, constructor arguments, catch clauses could increase visual clutter and verbosity. But if anything that can assist any future developer or any maintainer perform their jobs faster, reliably and effeciently, we should make use of that.

But, of course, it's all personal preference ;-) I would love to hear your opinions on this regard.

