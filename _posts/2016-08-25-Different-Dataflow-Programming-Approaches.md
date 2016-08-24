---
layout: post
title: "Different Dataflow Programming Approaches and Comparison with Kura Wires"
author: amit
modified:
share: true
comments: true
excerpt: "Several Visual Dataflow Programming Frameworks and Comparison"
tags: [Eclipse Kura, Kura Wires, OSGi, Node-RED, SAM Labs, SAM, glue.things, IIoT, IoT]
---

{% include _toc.html %}

## Overview

In my previous posts, I have discussed about the dataflow programming approach of **Kura Wires**. Furthermore, I have also discussed about the benefits that **Kura Wires** is expected to serve in the Industrial Internet of Things (IIoT) context.

Currently there already exists few frameworks which work on the dataflow programming model and also incorporate visual paradigm to develop Internet of Things (IoT) applications, for instance, Node-RED, glue.things, SAM Labs.

Today, in this post, I would like to focus on the differences amongst these frameworks, along with the comparison with **Kura Wires**.

## Node-RED

![alt text](https://developer.ibm.com/bluemix/wp-content/uploads/sites/20/2015/02/nodered.png "Node-RED")

**Node-RED** provides a browser-based flow editor that makes it easy to wire together devices, APIs, and online services by using the wide range of nodes in the palette. Flows can be then deployed to the Node.js runtime with a single click. **Node-RED** is a flow based approach to support various functionalities and smart devices. Functional blocks, so-called nodes can represent databases, functions or smart devices. These nodes can be wired together and build a functional graph. The nodes encapsulate some functionalities, while it is possible for the developer to implement functions within code, added to a node.

However, **Node-RED** is connecting miniature smart devices, for instance, lights and switches, the UI is essentially aimed for developers as it requires developers to write code snippets in the **Node-RED** composer UI to define bespoke functionality in a node. This constrains users without programming experience to implement interactions between these smart devices which they have not done before.

In addition, as of now it does not support communication with industrial devices for instance, Modbus, OPC-UA, Siemens S7 PLCs etc. which we are aiming to solve using **Kura Wires**. However, custom nodes are provided by many developers to communicate with OPC-UA and Modbus devices, it is still not the primary focus of **Node-RED** to communicate with industrial devices to process their telemetry data. In **Kura Wires**, our focus is to process machine production data through the use of different Logical blocks and Computational Blocks. We are also aiming to provide several reusable components as much as possible so that users will not need to write a single line of code to implement their own Industrial Internet of Things scenarios.


## glue.things

![alt text](https://s15.postimg.org/a5m9gcqjv/glue_things.png "glue.things")

**glue.things** is another framework for IoT application development that has some similarities to **Node-RED**.  In **glue.things**, a master device controls and monitors all participating nodes in a local network. Whenever a device joins the network, its special nodes are added into the flow editor of the master device and, made available to the developer.

However, **glue.things** composer does not work with industrial devices individually.

## SAM Labs

![alt text](http://mapprojectoffice.com/wp-content/uploads/2015/05/SAM_0055.jpg "SAM Labs")

**SAM 2** is an IoT development kit, based on a set of smart devices. All of them are connected per Bluetooth LE and are physically standalone smart devices, since a battery provides energy for each smart device to run autonomously. Each smart device provides a simple functionality which can be buttons, LEDs and speakers for example. The user can arrange these smart devices with the use of a visual programming interface, running on the computer. The program is using nodes and edges to connect smart devices and arrange their functions per drag and drop. Aside of hardware integration, software functionalities such as social media access are additionally integrated.

However, **SAM** does provide a great platform to teach the orchestration of hardware elements. The focus of **SAM** is the local development environment. Unlike **SAM**, **Kura Wires** focuses on the Industrial IoT application development which is a continual changing environment. Furthermore, the miniature smart devices of **SAM** are only limited to some basic functionalities and it is not possible to integrate off-the-shelf industrial smart devices containing diverse functionalities, for instance, industrial devices of different field protocols (Modbus, PROFINET, OPC-UA, CANbus etc.)

## Conclusion

The primary focus of these frameworks is to facilitate users with an easy to use tool to develop bespoke  Internet of Things scenarios, including functionalities of social network access. Unlike these tools, **Kura Wires** essentially focuses on easing the implementation of Industrial Internet of Things scenarios only. However, IoT developers can make use of the extension points in **Kura Wires** to develop custom Logical Blocks for Node-RED-like social access and many more. Furthermore, unlike these aforementioned tools, **Kura Wires** is completely based on Java and OSGi which would definitely motivate several Java developers to rapidly develop their own Industrial IoT applications. Our approach in **Kura Wires** hopefully would benefit users to focus on their Industrial Internet scenarios, rather than focusing on how to implement the scenarios.
