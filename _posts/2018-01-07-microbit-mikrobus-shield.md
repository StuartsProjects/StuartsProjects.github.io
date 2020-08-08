---
layout: post
title: "micro:bit Mikrobus Shield"
date: "2018-01-07"
---

I thought that a micro:bit would be a useful development platform for a simple sensor shield for Internet of Things monitoring applications. With the micro:bit you can quickly switch between programming in MicroPython or C\\C++ under the Arduino environment.

The micro:bit has just enough I\\O pins to allow for a single Mikrobus socket. These sockets allow the shield to be used with a range of plug in modules, including radio devices and the sockets eliminate the trailing wires that might otherwise be needed. The Mikrobus socket can be used for other devices and Mikroelectronica do a substantial range of compatible click modules, see link below;

[C](https://www.mikroe.com/click)[lick Modules](https://www.mikroe.com/click)

![microbit Mikrobus Shield Reduced](/images/microbit-Mikrobus-Shield-Reduced_thumb.jpg "microbit Mikrobus Shield Reduced")

I have Mikrobus PCBs available to make modules for several of the LoRa devices, such as RFM96, RFM98, DRF1272, DRF1278, build cost of these modules is around £7. Use of a LoRa radio device with the shield will allow for reception of sensor data over several kilometres.

A remote sensor will normally be battery powered and this shield can be configured to run for many months, or years on battery power alone.

A ready built  low cost (£1) Real Time Clock module is used to provide shutdown and wakeup functions. In sleep mode the micro:bit shield consumes a very miserly 0.001uA from the battery. The actual current is likely close to zero, but measuring below 1nA is not so easy. Sleep time can be up to a month.

The battery connects direct to the shield, this would typically be 3 x AA batteries or a single Lithium Ion battery. The shield has its own low drop out regulator supplying 3.3V to the micro:bit and Mikrobus socket. Using the standard 2 Alkaline batteries for the micro:bit supply can be marginal as the supply voltage may be as low as 2.4V for most of the batteries life.

There are pin connectors on board for typical I2C sensors such as the BME280 or discrete sensors such as the DHT11 and DS18B20. There is a connector intended for a serial connection such as a GPS. On the rear of the PCB is a micro SD card socket.

It is straightforward to add text displays such as the ILI9341 or Nokia 5110 via an I2C connected backpack I designed. The I2C display backpack uses an Arduino Pro Mini and is easy to build. The backpack is a good fit for the micro:bit, it allows a display to be added using no additional I\\O pins and the code library is very small. The software for the display backpacks has been tested on the micro:bit under MicroPython and the Arduino IDE.

![IMG_1440](/images/IMG_1440_thumb.jpg "IMG_1440")

![ILI9341](/images/ILI9341_thumb.jpg "ILI9341")

![Nokia5110](/images/Nokia5110_thumb.jpg "Nokia5110")

These are the simple commands to drive the display in MicroPython;

WriteCommand(ClearDisplay)  
SetCursorPosition(0,0)  
SetTextColour(White)  
WriteText('Hello World')

Driving the display in Arduino is much the same.

So far in the Arduino environment I have tested the functionality of an SPI LoRa module, reading and programming a GPS and SD card read write. And the displays above.

I will next be testing the MicroChip RN2483 which is popular for The Things Network (TTN) applications. I have designed a Microbus PCB to test this module.

**Stuart Robinson**
