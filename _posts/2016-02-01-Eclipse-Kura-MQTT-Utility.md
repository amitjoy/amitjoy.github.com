---
layout: post
title: "Eclipse Kura MQTT Client Utility"
author: amit
modified:
share: true
comments: true
excerpt: "Eclipse Kura MQTT Client utility tool for EDC Communication"
tags: [Java, Eclipse, Eclipse Kura, E4, MQTT]
---

{% include _toc.html %}

## Overview

This MQTT Client Application facilitates with all the MQTT related operations while using Eclipse Kura for IoT Application development.

The main motive of developing this application is to use Eclipse Kura’s CRUD like feature provided by CloudLet. This requires all the payloads to be encoded and decoded using Google Protocol Buffer. If we use any of the widely available MQTT Client Tools, we can only be able to use MQTT Payloads of simple string type. This utility application provides you with all the opportunities to encode and decode your provided payload on the fly.

### Interface

<figure>
	<img src="https://camo.githubusercontent.com/4598bf8b9a788eea2d985216ebbc1235cadb041d/68747470733a2f2f6269746275636b65742e6f72672f7265706f2f4c784c5870352f696d616765732f333336303338313239372d53637265656e25323053686f74253230323031352d30392d3131253230617425323031302e32322e3530253230616d2e706e67"></a>
</figure>
<br/>

<figure>
	<img src="https://camo.githubusercontent.com/b45d68ca3f683b93f0f5b12244394c366dd24a82/68747470733a2f2f6269746275636b65742e6f72672f7265706f2f4c784c5870352f696d616765732f333330323134383836302d53637265656e25323053686f74253230323031352d30392d3131253230617425323031302e31392e3139253230616d2e706e67"></a>
</figure>
<br/>

<figure>
	<img src="https://camo.githubusercontent.com/89b2327747cfc7ee072a6af2d6776e6321a74787/68747470733a2f2f6269746275636b65742e6f72672f7265706f2f4c784c5870352f696d616765732f323134383437353131302d53637265656e25323053686f74253230323031352d30392d3131253230617425323031302e32332e3537253230616d2e706e67"></a>
</figure>

### Github repo

<a markdown="0" href="https://github.com/amitjoy/kura-mqtt-client-utility.git" class="btn" target="_blank">Eclipse Kura MQTT Utility</a>
