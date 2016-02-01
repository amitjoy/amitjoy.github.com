---
layout: post
title: "Strategy Pattern using Java 8 Lambda Expression"
author: amit
modified:
share: true
comments: true
excerpt: "Implementing the strategy pattern using the powerful lambda expressions"
tags: [Java, Java8, Lambda, Stream]
---

{% include _toc.html %}

## Overview

We have learnt already about how to implement Command and Observer patterns using Java 8 Lambda Expression. Now, we would look into Strategy Pattern.

### Strategy Pattern

<figure>
	<img src="https://static.dzone.com/dz1/dz-files/strategy_pattern.png"></a>
</figure>

### The Problem

The problem is an excerpt from the book **Java 8 Lambdas by Richard Warburton**.

We have a requirement of compressing a specific file using different compression methods. We need an efficient way to add extensions to add several compressions strategies so that we need not to violate the **Open Closed Principle**.

### OOP way

Let's first solve it the usual way.

### Strategy

{% highlight java %}
public interface CompressionStrategy {

	public OutputStream compress(OutputStream data) throws IOException;

}
{% endhighlight %}

### Concrete Strategy - GZIP Compression

{% highlight java %}
public final class GzipCompressionStrategy implements CompressionStrategy {

	@Override
	public OutputStream compress(final OutputStream data) throws IOException {
		return new GZIPOutputStream(data);
	}

}
{% endhighlight %}

### Concrete Strategy - ZIP Compression

{% highlight java %}
public final class ZipCompressionStrategy implements CompressionStrategy {

	@Override
	public OutputStream compress(final OutputStream data) throws IOException {
		return new ZipOutputStream(data);
	}

}
{% endhighlight %}

### Client

{% highlight java %}
public final class Compressor {

	public static void init(final Path inFile, final File outFile) throws IOException {
		final Compressor gzipCompressor = new Compressor(new GzipCompressionStrategy());
		gzipCompressor.compress(inFile, outFile);

		final Compressor zipCompressor = new Compressor(new ZipCompressionStrategy());
		zipCompressor.compress(inFile, outFile);
	}

	private final CompressionStrategy strategy;

	public Compressor(final CompressionStrategy strategy) {
		this.strategy = strategy;
	}

	public void compress(final Path inFile, final File outFile) throws IOException {
		try (OutputStream outStream = new FileOutputStream(outFile)) {
			Files.copy(inFile, this.strategy.compress(outStream));
		}
	}

}
{% endhighlight %}

### Functional Way

You can assume that it's gonna be simpler than the previous design patterns. We just have to write the client class to pass the concrete compression strategies as behaviors to **Compressor** constructor argument as it accepts the **SAM (Single Abstract Method)** Interface **CompressionStrategy**.

### Client

{% highlight java %}
public final class Compressor {

	public static void init(final Path inFile, final File outFile) throws IOException {
		final Compressor gzipCompressor = new Compressor(GZIPOutputStream::new);
		gzipCompressor.compress(inFile, outFile);

		final Compressor zipCompressor = new Compressor(ZipOutputStream::new);
		zipCompressor.compress(inFile, outFile);

		final Compressor zipCompressor2 = new Compressor(data -> new ZipOutputStream(data));
		zipCompressor2.compress(inFile, outFile);
	}

	private final CompressionStrategy strategy;

	public Compressor(final CompressionStrategy strategy) {
		this.strategy = strategy;
	}

	public void compress(final Path inFile, final File outFile) throws IOException {
		try (OutputStream outStream = new FileOutputStream(outFile)) {
			Files.copy(inFile, this.strategy.compress(outStream));
		}
	}

}
{% endhighlight %}
