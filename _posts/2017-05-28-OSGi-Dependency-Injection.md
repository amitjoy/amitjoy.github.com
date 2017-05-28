---
layout: post
title: "OSGi Dependency Injection"
author: amit
modified:
share: true
comments: true
excerpt: "Practices and Quirks"
tags: [OSGi, Java, Modularity, Declarative Services, Felix Dependency Manager, Services]
---

{% include _toc.html %}

## Overview

OSGi follows a standard service model paradigm. This is required because Java shows how hard it is to write collaborative with only class sharing. The standard solution in Java is to use factories that use dynamic class loading and statics. For example, if you want a Factory, you call the static factory method Factory.newInstance(). Behind that façade, the newInstance methods tries every class loader trick to create an instance of an implementation subclass of the BuilderFactory class.

The solution to all these issues is simply the OSGi service registry. A bundle can create an object and register it with the OSGi service registry under one or more interfaces. Other bundles can go to the registry and list all objects that are registered under a specific interfaces or class. For example, a bundle provides an implementation of the Builder. When it gets started, it creates an instance of its BuilderFactoryImpl class and registers it with the registry under the BuilderFactory class. A bundle that needs a BuilderFactory can go to the registry and ask for all available services with the BuilderFactory class. Even better, a bundle can wait for a specific service to appear and then get a callback.

A bundle can therefore register a service, it can get a service, and it can listen for a service to appear or disappear. Any number of bundles can register the same service type, and any number of bundles can get the same service. This is depicted in the following figure.

![alt text](https://www.osgi.org/wp-content/uploads/services.png "OSGi Service Oriented Architecture")

## Well-known Dependency Injection Methods

1. Declarative Service
2. ServiceTracker and ServiceTrackerCustomizer
3. Usual way of getting ServiceReferences
4. ServiceListener
5. ServiceFactory
6. iPOJO
7. Felix Dependency Manager

## Whiteboard Pattern

The whiteboard pattern leverages the OSGi framework’s service registry instead of implementing a private registry as required by the listener pattern. Instead of having event listeners track event sources and then register themselves with the event source, the whiteboard pattern has event listeners register themselves as a service with the OSGi framework. When the event source has an event object to deliver, the event source calls all event listeners in the service registry.

Remarkably, the event source is not registered with the framework as a service. This makes bundles using the whiteboard pattern significantly smaller and easier to implement. The inter-bundle dependency between the event source and the event listener is handled by the framework and requires almost no code in the event source and event listener bundles. 

![alt text](https://s11.postimg.org/nxzpxyxoz/Bildschirmfoto_2017-05-10_um_10.04.07.png "OSGi Whiteboard Pattern")

The following Dependency Injection methods follow a whiteboard pattern

1. Declarative Services
2. ServiceTracker and ServiceTrackerCustomizer
3. ServiceFactory

## Declarative Services

Declarative Services makes writing a service implementation as simple as writing a POJO with a few annotations. Though there are other systems that do similar injections as Declarative Services, these other systems ignore time and dependencies. By handling time and (dynamic) dependencies without any code overhead OSGi provides a toolbox that is as innovative as objects were in the nineties.

## ServiceTracker Useful Methods

```java
public class ServiceTracker<S, T> implements ServiceTrackerCustomizer<S, T> {
.....
 public void open() {..}
 public void open(boolean trackAllServices) {..}
 public T getService() {..}
 public Object[] getServices() {..}
 public T waitForService(long timeout) throws InterruptedException {..}
 public void close() {..}
....
}
```

```java
public interface ServiceTrackerCustomizer<S, T> {
 public T addingService(ServiceReference<S> reference);
 public void modifiedService(ServiceReference<S> reference, T service);
 public void removedService(ServiceReference<S> reference, T service);
}
```

## ServiceTracker Example

```java
public final class ServiceTrackerExample {
 
 private ServiceTracker logTracker;
 
 void init() {
   logTracker = new ServiceTracker(bc, LogService.class.getName(), null);
   logTracker.open();
 }
 
 LogService getLog() {
   return (LogService)logTracker.getService();
 }
  
 void test() {
   getLog().doLog(...);
 }
 
 void destroy() {
   logTracker.close();
 }
 
}
```

## ServiceReference Example

```java
public final class ServiceReferenceExample {
 
 private BundleContext bc = ...;
 
 void doSomething() {
    final ServiceReference sr  =  bc.getServiceReference(HttpService.class.getName());
    if(sr != null) {
        final HttpService http = (HttpService)bc.getService(sr);
        if (http != null) {
            http.registerServlet(....)
        }
    }
 }
 
 void destroy() {
   bc.ungetService(sr)
 }
 
}
```

## Quirks

These API can easily be misused if proper precautions have not be taken care of. Few examples have been demonstrated as follows.

### Example 1

```java
public final class ServiceReferenceExample {
 
 private final BundleContext bc;
 
 public ServiceReferenceExample() {
  bc = FrameworkUtil.getBundle(getClass()).getBundleContext();
 }
 
 void doSomething() {
    final ServiceReference sr  =  bc.getServiceReference(HttpService.class.getName());
    if(sr != null) {
        final HttpService http = (HttpService)bc.getService(sr);
        if (http != null) {
            http.registerServlet(....);
        }
    }
 }
 
 void destroy() {
   // bc.ungetService(sr)
 }
 
}
```

The getService(..) call increases a HttpService use counter of this bundle. OSGi Service Registry keeps track of the service use counters to optimize service injections and cleanups. If we forget to call ungetService(..), the service counter will never be decremented and it will result in optimization issues.

A bundle's use of a service object obtained from this getService(..) method is tracked by the bundle's use count of that service. Each time the service object is returned by getService(..) the context bundle's use count for the service is incremented by one. Each time the service object is released by ungetService(..) the context bundle's use count for the service is decremented by one.

### Example 2

```java
public final class ServiceReferenceExample {
 
 private final BundleContext bc;
 private ServiceReference<HttpService> sr;
 
 public ServiceReferenceExample() {
  bc = FrameworkUtil.getBundle(getClass()).getBundleContext();
 }
 void doThis() {
    sr  =  bc.getServiceReference(HttpService.class.getName());
    if(sr != null) {
        final HttpService http = (HttpService) bc.getService(sr);
        if (http != null) {
            http.registerServlet(....);
        }
    }
 }
void doThat() {
  if(sr != null) {
        final HttpService http = (HttpService) bc.getService(sr);
        if (http != null) {
            http.registerServlet(....);
        }
  }
}
 void destroy() {
   bc.ungetService(sr);
 }
 
}
```
In this example, multiple calls to getService(..)  result in the service use counter to be incremented by 1 and finally the service count will be 2 in this example. The call to ungetService(..) will only decrement the service use counter by 1.

### Example 3

```java
public final class ServiceReferenceExample {
 
 private final BundleContext bc1;
 private final BundleContext bc2;
 
 public ServiceReferenceExample() {
  bc1 = FrameworkUtil.getBundle(getClass()).getBundleContext();
  bc2 = FrameworkUtil.getBundle(A.class).getBundleContext(); //A.java in other bundle and has been imported
 }
 
 void doSomething() {
    final ServiceReference sr  =  bc1.getServiceReference(HttpService.class.getName());
    if(sr != null) {
        final HttpService http = (HttpService)bc1.getService(sr);
        if (http != null) {
            http.registerServlet(....)
        }
    }
 }
 
 void destroy() {
   bc2.ungetService(sr)
 }
 
}
```

In this example, the BundleContext that invokes getService(..) will have a service count of 1 for HttpService. On the other hand, the BundleContext that calls ungetService(..) will not result in decrementing the service count as the service count is already 0 for the bundle in which A.java resides in. And the service count that invokes getService(..) will remain 1.

### Example 4

```java
public final class ServiceReferenceExample {
  
private volatile HttpService httpService;
 
 public ServiceReferenceExample() {
   final ServiceTracker<?, ?> serviceTracker = new CustomServiceTracker(
                    Activator.getBundleContext());
   serviceTracker.open();
 }
 void doSomething() {
    ...
 }
 private final class CustomServiceTracker extends ServiceTracker {
 
        public DbManagerServiceTracker(BundleContext context) {
            super(context, HttpService.class.getName(), null);
        }
 
        @Override
        public Object addingService(ServiceReference reference) {
            httpService = (HttpService) context.getService(reference);
            return httpService;
        }
 
        @Override
        public void removedService(ServiceReference reference, Object service) {
            context.ungetService(reference);
            dbManager = null;
        }
    }
}
```

A typical usage that developers do quite a often. It could have simply be done by calling getService() on the ServiceTracker. It is not easily maintainable to extend ServiceTracker. We must implement ServiceTrackerCustomizer if and only if we need specific operations to be performed while tracking services.

### Example 5

```java
public final class ServiceReferenceExample {
 
 private volatile DBManager dbManager;
 
 public ServiceReferenceExample() {
  final ServiceTracker<?, ?> serviceTracker = new ServiceTracker(
                    Activator.getBundleContext(), DBManager.class.getName(), null);
  serviceTracker.open();
 }
 
 void doSomething() {
    dbManager = serviceTracker.getService();
    dbManager.save()
 }
 
}
```

If the ServiceTracker is not required anymore to track any changes, it is a good practice to close it.

## Best Practices (according to me and subject to change)

1. Always unget ServiceReference after you get the service instance
2. Unget the ServiceReference on the same BundleContext that requested it
3. Be careful to call getServiceReference() on BundleContext as it returns the first found ServiceReference. Call getServiceReferences() instead.
4. Programmer's Nightmare: Multiple Threads request service on the same BundleContext. In this case, the thread share the same BundleContext instance.
5. Always close the ServiceTracker if you don't need to continue listening
6. Don't use multiple BundleContexts to request ServiceReferences
7. If service consumption must not be manipulated on demand, switch to Declarative Services or Felix DM
8. ServiceTracker can listen to multiple instances of the requested service. It is therefore better to call getServiceReferences() instead as you don't 9. know how many services will be tracked (unless you are 100% sure)
10. Once you call, getServiceReferences() on ServiceTracker, then follow #1 to #3
11. Don't override ServiceTracker. Implement ServiceTrackerCustomizer instead. It is more consumer-friendly.
