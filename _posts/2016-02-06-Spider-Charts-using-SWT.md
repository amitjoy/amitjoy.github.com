---
layout: post
title: "Spider Charts using SWT"
author: amit
modified:
share: true
comments: true
excerpt: "Easy to use library for generating Spider Charts using SWT"
tags: [Java, Spider Chart, SWT, Radar Chart, Eclipse RCP, Eclipse, JFace]
---

{% include _toc.html %}

## Overview

In today's world, we have several tools to visualize data in different forms of Charts but if you want to visualize multivariate data in the form of a two-dimensional chart, then we can make use of **Spider Charts**.

While working on a RCP Application, I stumbled upon a requirement of visualizing multivariate data using Spider Charts. I always abide by the rule to use open source libraries whenever needed.

### Eclipse BIRT

Eclipse BIRT is a famous open-source framework to generate data visualizations and reports. Amongst its several functionalities, it provides the opportunity to generate Spider Charts.

*So, to generate only Spider Charts, do we have to use the complete framework? Seriously! Are you kidding?*

Unfortunately I haven't found any solution yet that is only targeted to generate **Spider Charts** using SWT.

### Spider Chart SWT

To overcome the difficulties in generating **Spider Charts**, I have started off with a basic library that would only generate **Spider Charts**.

In addition to it, I have incorporated Java 8's Fluent API to use the library in more efficient way.

### Sample Usage

{% highlight java %}
@SpiderChartPlot(name = "iPhone 6", areaColor = DARKORCHID)
public final class IPhone {

	@DataPoints
	public double[] dataPoints() {
		final double[] data = { 4, 3.5, 4, 4.6, 5 };
		return data;
	}

}
{% endhighlight %}

{% highlight java %}
@SpiderChartPlot(name = "Nexus 6", areaColor = OLIVE)
public final class Nexus {

	@DataPoints
	public double[] dataPoints() {
		final double[] data = { 4, 3, 3, 4.1, 3 };
		return data;
	}

}
{% endhighlight %}

{% highlight java %}
// Create the objects of the annotated classes
final Supplier<Object> iPhoneData = IPhone::new;
final Supplier<Object> nexusData = Nexus::new;

final SpiderChartViewer viewer = SpiderChartBuilder.config(shell, settings -> {
    // Add title and legends to the Spider Chart
    settings.title(title -> title.text = "Smartphone Comparison Scale").legend(legend -> {
        legend.addItem(iPhoneData);
        legend.addItem(nexusData);
    }).plotter(plotter -> {
        // Add the Axes (name, maximum scale, minimum scale)
        final AxesConfigurer configuration = new AxesConfigurer.Builder().addAxis("Battery", 5, 0)
                .addAxis("Camera", 5, 0).addAxis("Display", 5, 0).addAxis("Memory", 5, 0).addAxis("Brand", 5, 0).build();
        plotter.use(configuration);
    });
}).viewer(chart -> {
    // Add the different data objects
    chart.data(firstData -> firstData.inject(iPhoneData)).data(secondData -> secondData.inject(nexusData));
});
{% endhighlight %}

### Sample Output

<figure>
	<img src="http://s10.postimg.org/h5j8mqb61/Screen_Shot_2016_02_07_at_12_19_09_AM.png">
</figure>

### Github Repo

<a markdown="0" href="https://github.com/amitjoy/Spider-Chart-SWT.git" class="btn" target="_blank">Spider Chart SWT</a>

**N.B:** Pull Requests are encouraged. :)
