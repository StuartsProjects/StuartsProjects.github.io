---
layout: post
title: "More ESP32 adventures"
date: "2018-09-09"
---

I have shown in a previous post that it had been possible to cut the deep sleep current of a ESP32 module, with a LoRa device, FRAM and display down to the 8uA region by careful attention to pull up resistors and type of display.

I thought it would be useful to be able to include a Micro SD card into my projects. For the trackers it would be very handy to be able to log GPS data to an SD card. This could be useful if for some reason the LoRa radio link stopped working. For the same reason this would be a useful backup for remote sensor or data gathering applications.

The current taken by micro SD cards seems to vary greatly, the one I was using was consuming 200uA or more when inactive, which would make battery life poor.

I tried switching off the power to just the SD card, but that had the effect of dragging pins on the SPI bus low which increased the sleep current of the LoRa device.

A different approach was needed.

I built up an ESP32 board that used a MOSFET to turn off the VCC power rail to all the devices external to the ESP32, LoRa module, display, FRAM, BME280 sensor and micro SD card.

The picture shows the prototype in action, sleep current was measured at 11uA, and that includes a resistor network to measure the battery voltage and a charger IC to allow for solar battery charging.

This power switching approach also made the software side an easier, it was not necessary to take all the attached devices through a shutdown sequence.

Of course this does mean that all devices need to be re-initialised every time the ESP32 comes out of sleep mode but this is not a difficult issue to deal with.

![Locator ESP32 Front](/images/Locator-ESP32-Front_thumb.jpg "Locator ESP32 Front")
![Locator ESP32 Rear](/images/Locator-ESP32-Rear_thumb.jpg "Locator ESP32 Rear")