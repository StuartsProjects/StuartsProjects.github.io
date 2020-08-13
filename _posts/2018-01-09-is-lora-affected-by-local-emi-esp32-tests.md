---
layout: post
title: "Is LoRa affected by local EMI ? - ESP32 Tests."
date: "2018-01-09"
---

It was mentioned in a previous post that a LoRa device can be significantly affected, i.e. performance reduced, by the presence of local Electro Magnetic Interference (EMI) from the power switches in USB power banks, read here for more detail;

[The Perils of USB Power Banks](https://stuartsprojects.github.io/2017/02/28/the-perils-of-usb-power-banks.html)

The power banks tested could reduce the link budget by up to 20dB, equivalent to cutting receive range\\distance by a factor of 10. This impacts portable devices in the main where the LoRa device antenna is close to the source of the EMI.

I did mention in the above post that in equipment where the antenna is local to the driving Micro controller then the EMI that produces can also reduce link performance. The standard reference micro-controller is an ATMega328P running at 8Mhz or 16Mhz with linear power supplies. When compared to a Arduino DUE which runs at 80Mhz and uses switching supplies, the DUE lost around 5dB in link performance, which cuts receiver range in about half, so it is a significant effect.

The new ESP32 based controllers, look very attractive for LoRa projects. Lots of Flash and RAM, up to 160Mhz processor speed, built in Wi-Fi and Bluetooth. There are also some modules such as the Wemos Lolin32 that have built in charger support for a local lithium battery. These modules are native 3.3V, so none of the dark ages stuff and faff of using 5V Arduino Unos or Megas.

My Wemos ESP32 arrived and I needed a way to compare a ESP32 receiver set-up with a reference ATMega one. Using two different board and LoRa devices could introduce errors if the performance of the two different LoRa devices was not the same.

Several of my projects use LoRa devices in breadboad friendly socket style sockets so I was able to breadboard first a ESP32 module then a Arduino Pro Mini module using the same LoRa device plugged into the breadboard. The antenna was kept the same as well.

I ran the test first with the ATMega and ESP32 with LoRa parameters of 434Mhz, BW62500, SF12 which are long range settings. I then tested both micro controllers at BW125000 and SF8. The graph of results is below. In the graph a decrease in performance will shift the trace to the left.

![ATMega versus ESP32](/images/ATMega-versus-ESP32_thumb.jpg "ATMega versus ESP32")

Some details of how the tests are carried out will be found here;

[Can we improve LoRa device performance ?](https://stuartsprojects.github.io/2018/01/09/can-we-improve-lora-performance.html)

At SF12 the red line of the ESP32 results is around 3.5dB behind the blue ATMega one, so it looks like the EMI from the faster ESP32 is degrading LoRa performance by that amount.

At SF8 the EMI has a lesser affect, with the ESP32 only loosing about 1dB when compared to the slower ATMega.

Completely screening the receiver circuit and supply can mitigate the amount of EMI the LoRa device sees.

I have one of the ESP32 modules with built in LoRa and OLED display on order and will be checking its performance versus a standard ATMega set-up.

**Stuart Robinson**

**January 2018**
