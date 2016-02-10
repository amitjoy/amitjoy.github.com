---
layout: post
title: "Concurrent Collection Mechanisms"
author: amit
modified:
share: true
comments: true
excerpt: "The different mechanisms in concurrent collections"
tags: [Java, Collection, Concurrency, Iterator]
---

{% include _toc.html %}

## Overview

In our daily day-to-day programming life, we need to use collections. But I believe, the choice of collection should be made carefully and that's why in Java, we have huge array of different collections.

In addition, Java 5 has introduced lots of new thread-safe collections such as **ConcurrentLinkedQueue**, **ConcurrentSkipListMap**, **CopyOnWriteArrayList** etc.

### Benefits

The benefits of using these concurrent collections include internal synchronization. Programmers don't require to impose client side locking. This kind of external synchronization is not required at all while using these concurrent collections. For improved thread-safety, the concurrent collections yield better performance. And hence, it is advisable to use such collections whenever we need.

## Mechanisms

The concurrent collections incorporate several mechanisms under the hood to obtain thread safety. The mechanims are discussed below.

### Copy-on-Write

The collections that make use of **Copy-On-Write**, uses **Arrays** as their backing data containers. The backing array is immutable and hence any change or modification to the data contained in such a collection will result in a new backing array by copying the data from the previous one.

**Examples**: *CopyOnWriteArrayList*, *CopyOnWriteArraySet*

**Use-case**: *Reads dominate over writes*

### Compare-and-Sweep

This mechanism is mainly used in such collections where internal computation on its values is a requirement. Let's consider we have a value that needs to operated on for a computation. We would have incorporated traditional synchronization on the whole computation logic but using **Compare-and-Sweep**, it caches a local copy of the concerned variable. The computation is then executed on the actual variable (*not the copy*). Whenever we need to update the value, the local value is checked whether it still has the same value as it contained at the beginning. If so, then the value is updated and if not, then it is considered to be updated by other thread. If it is updated by another thread, the mechanism retries to compute on the new updated value again.

**Examples**: *ConcurrentLinkedQueue*, *ConcurrentSkipListMap*

### Using Lock

Few collections in the Java collections framework make use of the **Java 5 Locks** mechanisms to provided internal synchronization.

**Examples**: *ConcurrentHashMap*, *BlockingQueue*

## Iterators

While traversing through a container, it is necessary for client code to use iterators on the collection. Due to incorporation of the aforementioned mechanisms, the policies of iterators have been changed a bit.

### Fail-Fast Iterator

This is the basic type of iterator which fails to modify items of any collection while traversing. This type of iterator throws **ConcurrentModificationException** on such scenarios.

### Snapshot Iterator

This iterator unlike **Fail-Fast** iterator doesn't report **ConcurrentModificationException**. The previously discussed **Copy-on-Write** collections yield such **Snapshot** iterators. If you recall the **Copy-on-Write** mechanism, it creates new backing array on modification of any item in the collection. That's why this kind of iterator only reads the values from one of these backing arrays without any danger of these items being modified by another thread.

### Weakly Consistent Iterator

The aforementioned **Compare-and-Sweep** collections use this kind of iterator. According to this iterator's policy, it can accommodate changes partially and hence it doesn't guarantee all the changes to be reflected. Like **Snapshot** iterator, it also doesn't throw **ConcurrentModificationException**.

Few collections like **DelayQueue**, **PriorityBlockingQueue** incorporate the **Weakly Consistent** iterator but these changes have been included in Java 6 but before Java 6, these collections use **Fail-Fast** iterators. Hence, to use these collections in thread-safe manner before Java 6, you have to utilize the old-school external synchronization methods.
