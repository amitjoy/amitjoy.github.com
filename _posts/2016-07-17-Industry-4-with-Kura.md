---
layout: post
title: "Kura Wires"
author: amit
modified:
share: true
comments: true
excerpt: "Industry 4.0 with Eclipse Kura"
tags: [Eclipse Kura, Kura Wires, OSGi]
---

{% include _toc.html %}

## Industrial Internet of Things

The Industrial Internet of Things (IIoT) is an evolution of existing technologies that enables end users
to improve processes, drive productivity, and maintain an edge in our increasingly competitive global economy.
The IIoT requires technology that can solve the interoperability challenges of device connectivity by reliably
and securely delivering data across the public domain at a much larger scale than previously thought possible.

## Eclipse Kura in IIoT

In the context of IoT application development, Eclipse Kura is an Eclipse IoT Open Source project that provides a platform for building IoT service gateways. It is a smart application container that enables remote management of such service gateways and provides a wide range of APIs for allowing you to write and deploy your own IoT application.

### Kura Wires

To overcome some challenges incorporated in building Industrial IoT applications, Eclipse Kura is soon offering a new feature, called **Kura Wires** essentially focuses on preventing users from having to continually reinvent the wheel. The objective of Kura Wires is to wire reusable components together which eventually allows configurable cooperation between these components.

Kura Wires incorporates Dataflow Programming Model in which every node in a dataflow graph represents either the field objects attached to an IoT service gateway or any computational capability to act upon the data from the field object. A developer can make use of Kura Wires to **visually** or **programmatically** connect blocks instances together, in order to define a dataflow graph that will consume data, process it and, eventually, publish it to the cloud.

## EclipseCON Talk

Due to the recent development of Kura Wires, I have proposed a session on Kura Wires in EclipseCON 2016. The talk on Kura Wires comprises a session with a comrehensive presentation of real Industry 4.0 use cases that will utilize sensors connected via Modbus or OPC-UA. The use-case scenarios would be modeled and implemented using Kura Wires with a complete dataflow model.

[The EclipseCON Talk Link](https://www.eclipsecon.org/europe2016/session/industry-40-eclipse-kura)

## Conclusion

During our research on Kura Wires, we have so far discovered the benefits of our approach which Kura Wires follows. We believe in industrial environment, it would help the IIoT deliver on its promise of improved operational efficiency through such application.

**There Will Be More To Come Soon...**
