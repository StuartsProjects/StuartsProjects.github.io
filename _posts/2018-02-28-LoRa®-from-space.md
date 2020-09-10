---
layout: post
title: "The first LoRa® received from Space !"
date: "2018-02-28"
---

During the The Things Network (TTN) conference in Amsterdam recently Thomas Telkamp arranged to borrow some time on the maritime satellite NORSAT2 and used it’s SDR to transmit LoRa® as the satellite passed over Amsterdam.

The LoRa® was transmitted on 162Mhz at 26dBm (400mW) into an approximate 6dB gain steerable yagi which was pointed to the horizon and at Amsterdam during the pass, data rate was 292bps.

The TTN receiver was a standard Microchip LoRa® 169Mhz device with a simple antenna that was placed on the roof of a two storey building in the industrial area just to the North West of Amsterdam center. The LoRa® was received as soon as the satellite cleared the horizon, a range of 2763km.

There was a challenge (prize) to be the first one to receive the LoRa® during the conference. One of my own very simple handheld Arduino Pro Mini receivers fitted with a 433Mhz LoRa® module (i.e. the wrong one for the frequency) and a telescopic whip antenna was good enough to receive the first LoRa® on 162Mhz. The insertion loss of 162mhz into a 433Mhz receiever had been estimated by Thomas at around 20dB!  The receiver was just left propped against the window of my hotel bedroom . I received packets close to the limit of reception with an SNR of -18dB.

![LoRa® from Space](/images/LoRa®-from-Space_thumb.jpg "LoRa® from Space")
 

The Video of the ‘LoRa® from Space’ presentation can be viewed here, my appearance is at 25:40;

**[https://www.youtube.com/watch?v=sNsLp0\_LIRs](https://www.youtube.com/watch?v=sNsLp0_LIRs "https://www.youtube.com/watch?v=sNsLp0_LIRs")**
