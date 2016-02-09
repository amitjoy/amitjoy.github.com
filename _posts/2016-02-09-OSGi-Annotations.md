---
layout: post
title: "OSGi Annotations"
author: amit
modified:
share: true
comments: true
excerpt: "Faster OSGi Bundle Development using Annotations"
tags: [Java, OSGi, Bnd, BndTools]
---

{% include _toc.html %}

## Overview

Those of you who are comfortable in OSGi application development, you might be accustomed to using the **Eclipse PDE (Plugin Development Environment)** to develop OSGi Bundles. In addition to it, some of OSGi Developers prefer to use **Maven Bundle Plugin**. OSGi provides lots of benefits in regard to modular Software Development but it also incorporates some boiler plate tasks such as modifying manifest headers each and every time you add a package. If you want your package to be private, it needs to be added to the manifest.

*Do we have a solution?*

### OSGi Bundle Annotations

This is a RFC proposal to incorporate several annotation which expedites **OSGi Bundle Development**.

### OSGi enRoute

I have previously talked about the benefits of using OSGi enRoute for rapid application development using OSGi. It incorporates the reference implementation of the aforementioned **OSGi Bundle Annotations**.

Let's have a look on few snippets on how to use them.

## OSGi Annotations

### @Component

{% highlight java %}
@Component(name = "osgi.enroute.examples.led.controller.application")
public final class MQTTLedApplication implements ManagedService, REST {

	/**
	 * Extended REST Request for PUT Verb
	 */
	private interface SettingsRequest extends RESTRequest {
		Settings _body();
	}
  ........
}
{% endhighlight %}

This annotation is used to annotate a class which would be used as a **Declarative Service Component**. By default, it would expose this component as service implementations of the interfaces that it implements. Here, the service interfaces are **Managed Service** and **REST**.

### @Reference

{% highlight java %}
@Designate(ocd = LedGpioConfiguration.class)
@Component(name = "osgi.enroute.examples.led.controller.core")
public final class LedController implements ILedController, ConfigurationListener {

	/**
	 * Configuration Settings Holder
	 */
	private static Pin configurablePin;

	/**
	 * GPIO Configuration Service PID
	 */
	private static final String GPIO_CONF_PID = "osgi.enroute.examples.led.controller.core";

	/**
	 * Configuration Admin Service Reference
	 */
	@Reference
	private volatile ConfigurationAdmin configurationAdmin;
  .....
}
{% endhighlight %}

**@Reference** is used to inject OSGi service dependencies dynamically. Finally no pain in creating declarative service component descriptor file to inject such dependencies.

### @Activate

This annotation is closely related to service components. You can annotate methods with this annotation to mark it as an activator method for the **Service Component**

{% highlight java %}
@Designate(ocd = LedGpioConfiguration.class)
@Component(name = "osgi.enroute.examples.led.controller.core")
public final class LedController implements ILedController, ConfigurationListener {
....
/**
	 * Activation Callback
	 */
	@Activate
	public void activate() throws IOException {
		try {
			this.gpio = GpioFactory.getInstance();
		} catch (final Throwable e) {
			this.logService.log(LOG_ERROR, "pi4j native library missing");
		}

		if (nonNull(this.configurationAdmin)) {
			this.gpioConfiguration = this.configurationAdmin.getConfiguration(GPIO_CONF_PID);
		}
	}
.....
{% endhighlight %}

### @Deactivate

This can be used to annotate methods to mark as a deactivator method in **Service Component**.

### @ObjectClassDefinition and @AttributeDefinition

{% highlight java %}
@ObjectClassDefinition(name = "LED MQTT Topic Configuration")
public @interface LedConfiguration {

	/**
	 * MQTT Subscription Topic
	 */
	@AttributeDefinition(name = "MQTT Subscription Topic", description = "The subscription topic to be used to send messages for switching on/off the light")
	public String subscriptionTopic() default "osgi/enRoute/led/controller";

}
{% endhighlight %}

This annotation is used to annotate your custom interfaces which would contribute to **OSGi Metatype Configuration**.

### @Designate

After you create **OSGi Metatype Configruation**, it is necessary to listen to configuration updates. We can make use of this annotation to listen to the configuration changes.

{% highlight java %}
@Designate(ocd = LedConfiguration.class)
@Component(name = "osgi.enroute.examples.led.controller.application")
public final class MQTTLedApplication implements ManagedService, REST {
.....
}
{% endhighlight %}

This class implements **ManagedService** to listen to the changes of the **LedConfiguration**

### @BundleCategory

This annotation is used to categorize the bundle by providing the comma-separated list of category names.

### @BundleContributors

It says it all. You can use it to mention the list of contributors. The runtime bundle manifest will comprise **Bundle-Contributors** header.

### @BundleCopyright

Developers can make use of this to incorporate copyright specification information.

### @BundleDevelopers

It is as same as the **@BundleContributors** which is used to mention the list of names of developers.

### @BundleDocURL

Used to generate **Bundle-DocURL** header comprising URL pointing to documentation.

### @BundleLicense

It generates **Bundle-License** header which comprises the licensing information for the bundle distribution.

### License Annotations

OSGi enRoute includes some license specific annotations which indicate that the annotated class depends on the
specified license.

For example,

{% highlight java %}
@EPL_1_0
public final class Utils {
....
}
{% endhighlight %}

This indicates that **Utils** class depends on **Eclipse Public License**.

In addition to it, it provides huge array of licenses such as Apache Lincense, GPL, MIT, CPL, CDDL etc.

### @RequireCapability

This annotation is used to annotate other annotations to define the requirement and capabilities of a bundle.

{% highlight java %}
@RequireCapability(ns = ExtenderNamespace.EXTENDER_NAMESPACE, filter = "(&(" + ExtenderNamespace.EXTENDER_NAMESPACE
		+ "=osgi.enroute.webserver)${frange;" + WebServerConstants.WEB_SERVER_EXTENDER_VERSION + "})")
@Retention(RetentionPolicy.CLASS)
public @interface RequireWebServerExtender {}
{% endhighlight %}

The previous code snippet has been taken from OSGi enRoute for better comprehension. The aforementioned annotation **@RequireWebServerExtender** is annotated with **@RequireCapability**.

Now, let's look at how to use it.

{% highlight java %}
@RequireWebServerExtender
@Designate(ocd = LedConfiguration.class)
@Component(name = "osgi.enroute.examples.led.controller.application")
public final class MQTTLedApplication implements ManagedService, REST {

	/**
	 * Extended REST Request for PUT Verb
	 */
	private interface SettingsRequest extends RESTRequest {
		Settings _body();
	}
  ......
}
{% endhighlight %}

This class **MQTTLedApplication** is annotated with **RequireWebServerExtender** which specifies its requirement at runtime.

### @ProvideCapability

It works the same way as **@RequireCapability** works, instead it provides a capability at runtime.

### OSGi enRoute Specific

OSGi enRoute has taken the responsibility to take the hassles of OSGi Application Development off our shoulder. It provides all necessary abstractions to develop rapid OSGi applications. Amongst its several benefits, it provides huge array of annotations for **Angular JS Application** development, Scheduling, Authentication and Authorization, IoT Application Development etc.
