---
layout: post
title: "Convert a raw List to a generic List"
author: amit
modified:
share: true
comments: true
excerpt: "How to convert a raw List to its generic counterpart?"
tags: [Java]
---

{% include _toc.html %}

## Overview

While refactoring old code, you might have already encountered that the classes are inundated with lots of non-generic List. It is important for a codebase to evolve and that's why I would like to discuss about how to refactor your old non-generic List to generic List.

### Example

{% highlight java %}
public final class UnchekedCast {

	public static void main(final String[] args) {
		final List objectss = asList("a", "b", "c");
		final List<Object> objects = asList("a", "b", "c");
		final List<String> strings = ((List<String>) ((List<?>) objects));
		final List<String> stringss = ((List<String>) ((List<?>) objectss));
		System.out.println(strings);
		System.out.println(stringss);
	}

}
{% endhighlight %}

Here, you can clearly see to cast a generic {% highlight java %}List<Object>{% endhighlight %} to {% highlight java %}List<String>{% endhighlight %}, we have to take 2 steps. Initially, we have to safely cast it to unbounded wildcard List and then to the actual {% highlight java %}List<String>{% endhighlight %}. The same method applies for non-generic List or raw type List as it is as same as {% highlight java %}List<Object>{% endhighlight %}.

You would also get to see **Unchecked Cast** warning which informs you about the presence of cast from a generic type to a non-qualified type or the vice versa.
