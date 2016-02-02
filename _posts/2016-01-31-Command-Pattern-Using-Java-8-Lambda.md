---
layout: post
title: "Command Pattern using Java 8 Lambda Expression"
author: amit
modified:
share: true
comments: true
excerpt: "Implementing the command pattern using the powerful lambda expressions"
tags: [Java, Java8, Lambda, Stream]
---

{% include _toc.html %}

## Overview

Due to the current inclusion of Java 8 Functional Programming Paradigm in today's world, people still wonder how it could collaborate well with the existing object oriented approach. That's why I would love to demonstrate a concrete example of command pattern using Java 8 Lambda Expression.

### Command Pattern

<figure>
	<img src="http://www.giandavidealfano.com/wp-content/uploads/2012/11/command_pattern.gif"></a>
</figure>

### The Problem Domain

An editor or IDE has gallons of actions or commands such as open, close, save etc. Each and every time, someone needs to develop a new command for the IDE, it should conform to the Open-Closed principle - Open for Extension and Closed for Modification. So, now our design decision should be effective enough to allow extensions to the applications so that anyone can easily plug-in new command to the IDE.

### OOP way

First of all, I would love to tell you about how it can be achieved using the object oriented way.

### Command

{% highlight java %}
public interface Command {

	public void perform();

}
{% endhighlight %}

### Concrete Command - Open

{% highlight java %}
public final class Open implements Command {

	private final Editor editor;

	public Open(final Editor editor) {
		super();
		this.editor = editor;
	}

	@Override
	public void perform() {
		this.editor.open();
	}

}
{% endhighlight %}

### Concrete Command - Save

{% highlight java %}
public final class Save implements Command {

	private final Editor editor;

	public Save(final Editor editor) {
		super();
		this.editor = editor;
	}

	@Override
	public void perform() {
		this.editor.save();
	}

}
{% endhighlight %}

### Receiver

{% highlight java %}
public interface Editor {

	public void close();

	public void open();

	public void save();

}
{% endhighlight %}

### Concrete Receiver

{% highlight java %}
public final class MockEditor implements Editor {

	private final List<String> actions = Lists.newArrayList();

	@Override
	public void close() {
		this.actions.add("close");
	}

	@Override
	public void open() {
		this.actions.add("open");
	}

	@Override
	public void save() {
		this.actions.add("save");
	}

}
{% endhighlight %}

### Invoker

{% highlight java %}
public final class Macro {

	private final List<Command> commands;

	public Macro() {
		this.commands = Lists.newArrayList();
	}

	public void record(final Command command) {
		this.commands.add(command);
	}

	public void run() {
		this.commands.forEach(Command::perform);
	}

}
{% endhighlight %}

### Client

{% highlight java %}
public final class Client {

	public static void main(final String[] args) {
		final Macro macro = new Macro();
		final Editor editor = new MockEditor();
		final Command openCommand = new Open(editor);
		final Command saveCommand = new Save(editor);

		macro.record(openCommand);
		macro.record(saveCommand);

		macro.run();
	}

}
{% endhighlight %}

### Functional Way

So far, we have seen the object-oriented approach in solving our defined problem using Command Pattern. Now, we would try to solve it using Functional Programming Paradigm which I believe would be more concise and better solution.

### Command

{% highlight java %}
public interface Command {

	public void perform();

}
{% endhighlight %}

### Receiver

{% highlight java %}
public interface Editor {

	public void close();

	public void open();

	public void save();

}
{% endhighlight %}

### Invoker

{% highlight java %}
public final class Macro {

	private final List<Command> commands;

	public Macro() {
		this.commands = Lists.newArrayList();
	}

	public void record(final Command command) {
		this.commands.add(command);
	}

	public void run() {
		this.commands.forEach(Command::perform);
	}

}
{% endhighlight %}

### Client

{% highlight java %}
public final class Client {

	public static void main(final String[] args) {
		final Macro macro = new Macro();
		final Editor editor = new MockEditor();

		macro.record(() -> new Open(editor));
		macro.record(() -> new Save(editor));

		macro.run(); // 1st way

		macro.record(() -> editor.open());
		macro.record(() -> editor.save());

		macro.run(); // 2nd way

		macro.record(editor::open);
		macro.record(editor::save);

		macro.run(); // 3rd way
	}

}
{% endhighlight %}

### Benefits

You can clearly see that I didn't write any concrete implementation class of the commands. I have only passed those implementations as behaviors to the **record** method as it accepts **SAM (Single Abstract Method) Interface (Command)**.
