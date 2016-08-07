---
layout: post
title: "Kura Wires - A different perspective to develop IIoT applications"
author: amit
modified:
share: true
comments: true
excerpt: "Detailed overview on how Kura Wires tries a different approach to develop IIOT applications"
tags: [Eclipse Kura, Kura Wires, OSGi]
---

{% include _toc.html %}

## Overview

In my previous [post](https://dzone.com/articles/kura-wires), I have already discussed about our research work - **Kura Wires** with whcih we are aiming to solve some intricacies involved in implementing several use cases in Industrial IoT scenarios.

Today, I would like give you an overview on how **Kura Wires** would try to solve a specific and pervasive use case.

## Use case

The main objective of Kura Wires is to wire or connect reusable components together to allow for the configurable cooperation of these components. For instance, a temperature sensor can be connected to Kura-powered service gateway to publish its temperature data to the cloud platform in a timely manner. In a similar use case, industrial devices with different protocols can also be connected to the IoT service gateway, leveraging configurable options for publishing specific or filtered data to remote servers or cloud platforms for further aggregation and analysis.

So, today we would consider this scenario most often found in factories in which the operator or industrial engineer accumulates data from several PLCs connected to an IoT service gateway and sends these machine production data to the cloud integration platform for further aggregation and analysis.

![alt text](https://s7.postimg.org/73rm94pu3/scenario.png "Industrial IoT Scenario")

## Glossary

Before we start discussing about how we can solve implementing such a generic Industrial use case, we would like to introduce some terminologies required to understand **Kura Wires**.

1. **Composer UI** - It is the canvas area for Kura Wires in which the dataflow graph will be created.

2. **Logical Block** - A **Logical Block** is a visual element in the **Composer UI** which is represented as a node in the Kura Wires dataflow graph.

![alt text](https://s7.postimg.org/mebnn7xbv/logical_block.png "Logical Block")

So, inherently a **Logical Block** *can* (not always though) have *0 to n* number of inputs and *0 to n* number of outputs.

Few logical blocks can have either of them whereas some will have both of them.

3. **Computational Block** - A **Computational Block** is a **Logical Block**, capable of receiving, processing and emitting data to the connected downstream logical blocks. It can be, for example, data store, data filter, data publisher instances that will be used to manage data.

4. **Wire** - A **Wire** is a logical connection between the **Logical Block**s which allows to define a concrete data flow in Kura Wires.

5. **Asset** - An **Asset**  is a **Logical Block** that is capable of communicating with specific sensors and/or actuators of **Industrial Device** using specific protocol.

6. **Wire Graph** - A **Wire Graph** is a dataflow graph comprising several aforementioned **Logical Block**s which represents an Industrial IoT Application scenario.


## Wire Graph Example

So far, we have talked about several Kura Wires specific terminologies which we will be using throughout this post to understand more about Kura Wires.

Now, we would like to see how Kura Wires tries to take away the difficulties in implementing the aforementioned scenario. The following digram shows the **Wire Graph** implementation for our chosen scenario.

![alt text](https://s8.postimg.org/6jokzr1ad/kura_wires.png "Kura Wires Wire Graph")

Here, in this example, we can see several **Logical Block**s incorporated in the **Wire Graph**, for instance, different instances of **Timer**, different types of **Asset**, **Db Store**, **Db Filter** and **CloudPublisher**.

1. **Timer** - It is a specific **Logical Block** which is configured with a period and it triggers an event on every configured interval.

So, here **Timer1**, **Timer2** and **Timer3** are different instances of **Timer** logical block which are configured with different time intervals.

You can see that the different **Timer** instances are wired to different instances of **Asset**.

2. **Modbus Asset** - A **Modbus Asset** is an **Asset** representing a **Modbus Device** in the **Wire Graph**.

3. **OPC-UA Asset** - An **OPC-UA Asset** is an **Asset** representing an **OPC-UA Device** in the **Wire Graph**.

4. **S7 Asset** - An **S7 Asset** is an **Asset** representing an **S7 Device** in the **Wire Graph**.

5. **DB Store** - A **DB Store** is a **Computational Block** representing a database storage.

6. **DB Filter** - A **DB Filter** is a **Computational Block**, capable of filtering data by creating dynamic database views.

7. **Cloud Publisher** - A **Cloud Publisher** is a **Logical Block** which is responsible for publishing data to the configured **Cloud Platform**.

These different logical blocks in the Wire graph are properly configured, such as the interval in different instances of **Timer**, the different channels to read or write data from/to the industrial device, the database storage configuration to store data locally and the database view query to perform aggregation on the locally stored data and so on.

So, according to our developed **Wire Graph**, different **Timer** instances will emit an event which will trigger read and/or write operations on the configured channels and the data will be emitted to the downstream **DB Store** logical block which stores the data locally and emits an event to the downstream **DB Filter** logical block to filter the locally stored data. Thereafter, the filtered data will be sent to the wired **Cloud Publisher** logical block which publishes the data to the configured Cloud Platform.

## Conclusion

We strongly believe that such visual dataflow programming approach will help users to focus on their application scenario more rather than on writing applications in Eclipse Kura to do the same. Our approach in Kura Wires is very much extensible so that developers can even focus on developing their own **Logical Block**s to create a different **Wire Graph** to implement some different IoT scenario.

In my future blog post, I would discuss more about how to develop specific **Logical Block** to incorporate in **Kura Wires**.
