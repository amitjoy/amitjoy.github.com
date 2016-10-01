---
layout: post
title: "Kura Wires - A sneak peek"
author: amit
modified:
share: true
comments: true
excerpt: "Kura Wires and its first glance"
tags: [Eclipse Kura, Kura Wires, OSGi, Node-RED, SAM Labs, SAM, glue.things, IIoT, IoT]
---

{% include _toc.html %}

## Overview

Previously I have discussed about **Kura Wires** - the upcoming feature in Eclipse Kura which is aiming to solve the Industrial IoT challenges.

Today, in this post, I would like to show you a sneak peek on the beta version of **Kura Wires**.

## Kura Wires Composer UI

![alt text](https://s21.postimg.org/ll7cuqc1z/image.png "Kura Wires Composer UI")

This is the Composer UI of **Kura Wires** to implement Industrial IoT scenarios by creating **Wire Graph**. (Please refer to [Link](http://blog.amitinside.com/Kura-Wires-bits-and-pieces/) to know more about the different terminologies involved in **Kura Wires**)

![alt text](https://s14.postimg.org/sqzzial2p/image.png "Wire Graph")

This shows an example Industrial IoT scenario to accumulate and store telemetry data from Modbus PLC and publish it to the cloud platform.

![alt text](https://s9.postimg.org/xae91kwgf/image.png "Wire Graph with Configurable Parameters")

This partially shows the different configurable parameters for a **Modbus Asset**. Based on the driver implementation that the **Asset** uses, the configurable options get changed. Here what you see is strictly specific to Modbus Driver as I have associated a Modbus Driver implementation to the **Asset** in the **Wire Graph**.

## Conclusion

In my upcoming talk on **Kura Wires** in EclipseCON Germany, I would love to discuss the nitty-gritty details of **Kura Wires** and would love to present an end-to-end scenario using **Kura Wires**. I hope you would like the new feature in Eclipse Kura as it focuses on speeding up development of any IoT application scenario. Apart from these, we are also striving our best to create an ecosystem surrounding **Kura Wires**, where developers around the world would develop special purpose **Wire Component**s and **Driver**s that any IoT application developer can use from Eclipse Marketplace by dragging and dropping it to their Eclipse Kura installations as well.
