---
layout: post
title: "Testing OSGi Bundles"
author: amit
modified:
share: true
comments: true
excerpt: "Unit and Integration Test OSGi Bundles"
tags: [OSGi, RCP, Unit, Integration, Test]
---

{% include _toc.html %}

As an OSGi Developer, the problem to test bundles or the plugins in case of Eclipse RCP, makes me really mad. The decision to unit test or integration test the bundles involves several ways but there is no such distinction on a specific method to follow. This is why I am thinking to write a post on the available methods and its pros and cons.

## Overview

So, as I said, I am going to focus on the currently available methods to unit and integration test your OSGi Bundles and finally we would see the advantages and disadvantages of each and every method.

### The Different Ways

Until recently I am aware of 3 different ways to test OSGi bundles.

1. All test codes in a single bundle
2. All test codes in a separate bundle
3. All test codes in a fragment bundle

### Test codes in a single bundle

This is the most easy way to achieve testing in OSGi environment. Developers prefer to store the source codes in **src/main/java** whereas the test codes are kept inside **src/test/java**.

#### Benefits

This approach has the benefit that you have a single bundle classloader which works for you. There is no need to mangle with the classloaders of different bundles.

#### Drawbacks

This approach actually convolutes your application building process. Sometimes, people want to run the tests separately but in this scenario, the test codes are tightly coupled with the source bundle. This is why it is not a preferable approach.

### Test codes in a separate bundle

This approach is somewhat better as it solves the issue as found in the previous testing method. As far as I have seen, many open source projects also follow this way of testing.

#### Benefits

I personally like this of way of testing as it separates the source codes from the test codes. This eventually helps in customizing build process in a way to run test and source bundles differently.

There is a well known solution to test OSGi bundles in isolation and integration : http://www.everit.org/osgi-testrunner/

The solution also prefers this approach of testing OSGi Bundles. You can have a look on an elaborative documentation in the following link: http://cookbook.everit.org/tdd/index.html

#### Drawbacks

Even though it is one of my favorites, it still suffers from few potential problems. This methodology involves the two different bundle classloaders - the source bundle classloader and the test bundle classloader which is an overkill. In addition, the packages from the source bundle need to be exported so that it can be tested in the test bundle. It also suffers from the fact that the implementation packages are also exported. This actually pollutes the bundle manifest. Apart from it, non-public methods or the classes are not testable following this approach.

### Test codes in a fragment bundle

I believe this could be the actual solution people need to follow for testing bundles.

#### Benefits

This doesn't suffer from the 2 bundle classloaders as there exists only one classloader for the host as well as the fragment bundle.

#### Drawbacks

Apparently it should be the best solution but still according to my experience, I have seen it also suffers from few issues. Some very famous solutions to test OSGi bundles follows the notion of exposing test cases as OSGi services (ex. http://www.everit.org/osgi-testrunner/) but if your test codes are kept inside a fragment bundle, it is impossible to expose the test cases as OSGi services.

In addition, it is really tough to write a test suite for all your test fragment bundles. It involves quite a few hacks to load test classes from different bundles and run them in the OSGi container.

### Conclusion

I personally favor the **testrunner solutions** which does the main work for you to test the bundles in isolation (for unit test) and integration (for integration test). As I said, the decision is completely upto the developers. The choice still depends on the structure of the code base. Considering the general structure of a project source code, people might want to pursue the solution to separate test codes in a fragment bundle but it also incurs the problems as I have mentioned above.
