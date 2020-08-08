---
layout: post
title: "A Better Tracker Receiver"
date: "2017-01-14"
---

![Simple Receiver](/images/Simple-Receiver_thumb-2.jpg "Simple Receiver")

For some time I have been aware that my LoRa Trackers would be a much better system if it was possible to quickly and simply build a portable receiver. A DIY design would be preferred; this keeps the build cost low.

The current tracker boards, or one of the small 50mm x 50mm shield PCBs can be used for a portable receiver which works well enough but it can take a while to build and you do end up with a PCB that has a lot of external stuff such as displays, switches, GPS and the like. The current receivers are based on the Arduino Pro Mini and though this has adequate resources for the tracker transmitters, there is a distinct lack of program space and memory for a full featured receiver where you may want to include logging to SD card for instance.

One particular problem with developing a receiver is that it is di fficult to accommodate all the variants of different LoRa modules, GPSs and other devices. A modular design would be preferable, whereby it would be possible to plug in a range of different modules. 

![Mikrobus Modules](/images/Mikrobus-Modules_thumb-1.jpg "Mikrobus Modules")

I chose to use the Mikrobus standard for the modules, there are other module types but there is already a wide range of Mikrobus (Click) modules available, so the shield base would not be restricted to just one purpose. I could not find a Mikrobus module for the Dorji DRF1278F and Hope RFM9x LoRa devices I use in my trackers so I designed my own module PCBs for those and one for a GPS as well, see pictures. One other advantage of using modules in this way is that if the actual device, LoRa or GPS, is assembled onto the module and is faulty, there is no risk of causing damage to the main PCB if it has to be replaced.

An Arduino DUE shield base is native 3.3V (which is needed for most of the receiver components) has plenty of program space, memory, input\\output pins and it’s fast too. Using a pre built shield base has the distinct advantage that all the difficult SMT assembly is already done for you. The clone versions of DUE cost as little as £10.

The receiver then will be a shield PCB that plugs into an Arduino DUE. I planned for two versions of the shield, a small one to build a hand-held device intended for portable operation and a larger desktop shield that could take up to 5 Mikrobus modules.

A portable receiver is easy to assemble using the smaller two Mikrobus socket shield PCB. Fit the pin headers and a few other components and plug into an Arduino DUE base, see pictures below. If this is to be a development shield you should fit sockets for the Mikrobus modules and displays. For a compact low profile receiver just solder the Mikrobus modules and display direct to the shield PCB. 

![Bare Shield](/images/Bare-Shield_thumb-1.jpg "Bare Shield")

![Arduino DUE](/images/Arduino-DUE_thumb-1.jpg "Arduino DUE")

![MikroBus Shield Underside](/images/MikroBus-Shield-Underside_thumb.jpg "MikroBus Shield Underside")

The portable shield has the following features; they don’t all have to be fitted;

Two Mikrobus sockets.

Choice of display, Nokia 5110 LCD (very good out of doors), ST7755 TFT display or a SD1306 OLED.

5 Way Navigation Switch.

![Shield V3_1](/images/Shield-V3_1_thumb.jpg "Shield V3_1")

Micro SD card.

Pads for SPI or I2C FRAM memory.

Two LEDs and a Buzzer.

Header for HC06 Bluetooth module for link to Android mapping.

Audio output socket for AFSK uplink at 600 baud into FLDIGI.

Reset supervisor for DUE base.

Analogue voltage reference for DUE base.

The small shield has the option to fit a small power distribution PCB. This is to allow a cased unit with power switches to be built which will run from an internal USB power bank and charging from the USB programming connection. To facilitate a cased unit the PCB has connectors for an external SD card module, LEDs, displays, navigation switch and power switches.

There are two UART Serial headers on the underside of the small shield; one intended for a ESP01 WiFi device and another for a HC05\\06 Bluetooth module, either of which could be used for an external GPS. There will shortly be a Mikrobus module that can take the popular BME280 and BMP280 pressure and environment sensors, the DS18B20 temperature sensor or the DHT11 sensor. So with LoRa and sensor Mikrobus modules fitted together with an external Ublox GPS (£10ish on eBay) the shield would then be a fully functional and low cost HAB tracker transmitter.

A completely new version of the tracker receiver software is nearing completion and this allows it to be used for high altitude balloon (HAB) tracking or as a lost model locator. Fitted with an optional local GPS the receiver will calculate and display distance and direction to the remote tracker. Received telemetry can be logged to an SD card and the last GPS co-ordinates permanently saved to FRAM. The software integrates a serial terminal interface to remote control the distant HAB tracker. The receivers LoRa and frequency settings can be programmed in an over the air bind process received from the tracker transmitter or the tracker transmitter can be remotely programmed from the receiver. Thus it’s no longer necessary to unpack the tracker from a payload or model to make last minute adjustments to LoRa or frequency settings. 

The receiver has an audio uplink for an Internet connected PC to upload tracking data into Spacenear as well as a Bluetooth link to a mapping application on an Android phone or tablet.

When it’s all fully tested and there has been a live flight test, I hope to make the Shield PCB and the PCB of Mikrobus modules available for sale. Further Mikrobus PCBs are in progress for ESP01 Wifi, HC06 Bluetooth, NRF2401 RF and the various Bosch environment sensors. 

![Large Shield](/images/Large-Shield_thumb.jpg "Large Shield")

This is the large version of the Mikrobus shield, it also plugs into an Arduino DUE base. It can take the Nokia 5110 display or ILI9341 2.4” and 2.8” displays with SD card. There are 5 Mikrobus sockets, 3 on top 2 on the underside. Shown on the left with two LoRa modules, GPS and 2.8” display.

Plus here is a sneak preview of a really small matchbox sized LoRa receiver, it really is complete with LoRa device, display, GPS, battery and charger. It’s all you need to track a LoRa equipped HAB or lost model..

![Matchbox Receiver](/images/Matchbox-Receiver_thumb-1.jpg "Matchbox Receiver")
