---
layout: post
title: "High Altitude Balloon Tracking Software for SX12XX library – Part 3"
date: "2020-05-25"
---

## ESP32 tracker

![](/images/ESP32-Tracker.jpg)

The ESP32 has become a popular choice for Arduino projects, the processor can run at 240Mhz and it has plenty of memory plus built in Wi-Fi and Bluetooth. However the ESP32 does at 240Mhz use a lot of current. Using an example Arduino sketch that put the processor into a never ending while loop, I measured the current consumption of the ESP32 and compared it with the ATmega328P. At 240Mhz the ESP32 idle current is 62mA compared to 4mA for an 8Mhz ATmega328P, quite a large difference.

The GPS used with the tracker consumes about 20mA to 30mA (it varies up and down) so the approximate total power consumption for a high altitude balloon tracker (HAB) tracker would be;

ATmega328P GPS tracker 24mA to 30mA

ESP32 GPS tracker 82mA to 92mA

What happens when the ESP32 is used in a practical application such as the HAB tracker, how much more power does it use compared to an ATmega328P? I developed a ESP32 equivalent to the Easy pro mini board for ATmega328P and as with those boards you can plug in a LoRa module of choice;

![](/images/Easy-ESP32-1007x1024.jpg)

![](/images/Easy-ESP32-Bottom-884x1024.jpg)

I programmed up the ESP32 with the HAB tracker software using the ESP32 defaults of 240Mhz CPU speed. When the battery life test from part 1 was carried out the 100mAhr battery lasted lasted 138 minutes, compared to the 348 minutes of the ATmega328P doing the same test.

Fair enough you might think, after all the ESP32 processor is running at 240Mhz compared to the 8Mhz of the ATmega328P. A lot of tracker or sensor node applications just don't need a processor with such high speed capability, and the high power consumption is definitely not helpful, so initially the ESP32 looks very unsuitable for this type of application.

## **Slow down the ESP32?**

The Arduino IDE core for the ESP32, which is published by Expressif Systems, has various options of slowing the ESP32 down from the default of 240Mhz. For a simple tracker application do we really need to use a processor running at 240Mhz?

I selected the ‘CPU Frequency 10Mhz (40MHz XTAL)’ option but the HAB tracker program would not read the LoRa device although the GPS was read OK. The ‘CPU Frequency 20Mhz (40MHz XTAL)’ option did work with both LoRa and GPS, although the DS18B20 temperature sensor did not.

So I set the HAB tracker software off with the ESP32 set for 20Mhz CPU clock and the same 100mAhr battery connected. The battery lasted 303 minutes, so the 20Mhz CPU option adds 165 minutes to the battery life.

These results for the ESP32 were a surprise. The ATmega328P test had a battery life of 348 minutes (with the CPU idling at circa 4mA) so the ESP32 at a slow CPU speed of 20Mhz is not far behind.
