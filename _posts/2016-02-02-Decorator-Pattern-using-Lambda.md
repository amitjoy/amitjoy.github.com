---
layout: post
title: "Decorator Pattern using Java 8 Lambda Expression"
author: amit
modified:
share: true
comments: true
excerpt: "Implementing the decorator pattern using the powerful lambda expressions"
tags: [Java, Java8, Lambda, Stream]
---

{% include _toc.html %}

## Overview

I have previously posted few articles on implementing some useful patterns using the all new Java 8 Lambda Expressions. Today, I am going to discuss about **Decorator Pattern**

### Decorator Pattern

According to the Gang of Four, this pattern is useful when we require to add behavior to the individual object at runtime.

### Inheritence

You might be thinking that **Inheritence** could be the answer. But **Inheritence** is not suitable to add behaviors at runtime as it is statically applied to the entire class.

### Concrete Example

The following example has been taken from **SourceMaking**.

<figure>
	<img src="https://sourcemaking.com/files/v2/content/patterns/Decorator_example-2x.png"></a>
</figure>

*Assault gun is a deadly weapon on it's own. But you can apply certain "decorations" to make it more accurate, silent and devastating*

### The Problem Domain

Let's say, in Berlin, Germany, we have an Agency, called **Eval**. The purpose of this agency is to verify residents of Berlin in regard to several validation criteria. The agency has defined several validation criteria to verify the residents. After 20 years of its operation, the agency has been acquired by a big firm in Zurich, Switzerland. Now, this big firm wants to add their own validation criteria to validate residents of Zurich.

Now, to solve such a problem, we need to develop the application in such a way that it would facilitate extension to add more validators at runtime.

### Functional Way

### Candidate POJO

{% highlight java %}
public final class Candidate {

	private final String name;

	public Candidate(final String name) {
		checkNotNull(name);
		this.name = name;
	}

	public String getName() {
		return this.name;
	}

	@Override
	public String toString() {
		return MoreObjects.toStringHelper(this.getClass()).add("name", this.name).toString();
	};

}
{% endhighlight %}

### Crime Evaluator Criteria

{% highlight java %}
public interface CrimeEvaluator {

	public static Candidate evaluate(final Candidate candidate) {
		System.out.println("Evaluating candidate for Crime");
		return candidate;
	}
}
{% endhighlight %}

### Experience Evaluator Criteria

{% highlight java %}
public interface ExperienceEvaluator {

	public static Candidate evaluate(final Candidate candidate) {
		System.out.println("Evaluating candidate for Experience");
		return candidate;
	}

}
{% endhighlight %}

### Residence Evaluator Criteria

{% highlight java %}
public interface ResidenceEvaluator {

	public static Candidate evaluate(final Candidate candidate) {
		System.out.println("Evaluating candidate for Residence");
		return candidate;
	}

}
{% endhighlight %}

### The Client Application

{% highlight java %}
public final class Application {

	@SafeVarargs
	private static Function<Candidate, Candidate> decorateEvaluators(
			final Function<Candidate, Candidate>... evaluators) {
		return Stream.of(evaluators).reduce(Function.identity(), Function::andThen);
	}

	private static void evaluateCandidate(final Candidate candidate, final Function<Candidate, Candidate> evaluator) {
		System.out.println(evaluator.apply(candidate));
	}

	public static void main(final String[] args) {
		final Candidate candidate = new Candidate("AMIT");
		evaluateCandidate(candidate, decorateEvaluators(ResidenceEvaluator::evaluate, CrimeEvaluator::evaluate,
				ExperienceEvaluator::evaluate));
	}

}
{% endhighlight %}

### Conclusion

Now, as we said, **Decorator Pattern** is mainly used to add behavior at runtime, here in the aforementioned example, we can create any validator and pass it to the **decorateEvaluators** method call in **Application** class.

The **decorateEvaluators** method accepts variable argument of functional interface **Function**. The purpose of this method is to combine all the functions to one single **Function** so that it can directly be applied to the **Candidate** for evaluation/validation.

This pattern facilitates user to add behaviors to the object but not methods to the object's actual interface.
The Object's actual interface remains untouched while the responsibilities get added to the object.
