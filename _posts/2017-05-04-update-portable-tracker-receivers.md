---
layout: post
title: "Update - Portable Tracker Receivers"
date: "2017-05-04"
---

As I have commented on before, it’s a shame the Arduino DUE was not more suitable for a portable tracker receiver, cheap and powerful, but the loss in sensitivity due to local EMI getting into the LoRa antenna was too much. There was also similar a reduction noted with the GPS that have the integral ceramic antenna.

Its taken a while to go through all the testing and checking to be sure that the new receivers will work well, but I eventually finished and the initial PCB's are due in a couple of weeks.

There are planned to be two receivers, both have the same layout as the previous DUE based one you will have seen and use Mikrobus sockets for the LoRa device and GPS.

The first receiver is a shield that plugs into a standard Arduino Mega 2560 (as low as £6.50 delivered!!) and uses 4 x 8way logic level converters to shift between the 5V logic of the Mega and 3.3V logic used by all the receiver components. It should be quick to put together and the only SMT components are an I2C FRAM, a regulator and micro SD card socket. Plug in a display, my low cost Mikrobus LoRa and GPS modules, a battery into the Mega base power input, and your good to go. It did take a while to confirm that all the bits would work through logic level converters.

The second receiver is a single PCB, same layout as before, based on the ATMEGA 1284P running at 8Mhz. This is laid out to allow it to run from a small battery pack and eventually I plan to get a simple case for it made so it can be packaged up as a complete portable receiver. Although the Atmega 1284P is supposedly compatible with both the 328P and 2560 Atmel processors it took a while to check out that all the bits of hardware would work correctly within the Arduino IDE.

If the PCB's are OK, there will be a few for sale. The long term plan is to make the receivers available as a part kit, all the bits you need apart from the display, GPS, batteries and Mega 2560 base in the case of that receiver.

**Mikrobus Modules**

Whilst my boards are intended to be used as LoRa receivers with a GPS, the MikroBus sockets allow either board to be used for other purposes, there is a wide variety of modules available from Mikroelektronika;

[https://www.mikroe.com/mikrobus/](https://www.mikroe.com/mikrobus/)

The kits will contain the PCB's to make your own (very low cost) Mikrobus modules for the receiver. I currently have PCB's for Dorji DRF1278F and Hope RFM9x LoRa modules, two types of GPS, and one to take the low cost Bluetooth, ESP01 Wi-Fi and NRF24L01 Wireless modules.
