---
layout: post
category : Java
tagline: "Convert a raw List to a generic List"
tags : [Java]
---
{% include JB/setup %}

## Overview

While refactoring old code, you might have already encountered that the classes are inundated with lots of non-generic List. It is important for a codebase to evolve and that's why I would like to discuss about how to refactor your old non-generic List to generic List?

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

Here, you can clearly see to cast a generic List<Object> to List<Strin>, we have to take 2 steps. Initially, we have to safely cast it to unbounded wildcard List and then to the actual List<String>. The same method applies for non-generic List or raw type List as it is as same as List<Object>.

You would also get to see **Unchecked Cast** warning which informs you about the absence of **instanceof** check before each and every cast operation.

###Important

But remember, you can only check **instanceof** on **Reifiable Types**. Just to give you a glimpse on what Reifiable type means, it is a type which can be completely represented at run-time.

Eg, A primitive type, non-parameterized class or interface type, parameterized type of unbounded wildcard (List<?>), raw type (List), Array whose component is reifiable (List<?>[], int[], List[]).
