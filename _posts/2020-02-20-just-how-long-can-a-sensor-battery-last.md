---
layout: post
title: "Just how long can a sensor battery last?"
date: "2020-02-20"
---

Remote lora sensors are most often battery powered and a long battery life is desirable. If you have to keep changing batteries every few weeks, its wasteful, expensive and takes up a lot of time.

So we develop strategies to make a sensor operate with as little power as possible. A ATmega328P processor (as used in Arduino Pro Minis) running at 3.3V can have a deep sleep current of under 1uA. Add a good choice of voltage regulator, such as MCP1700, and the deep sleep current of the board will be around 2uA. That's good and would give a long battery life, the only significant use of power would then be when the processor is woken up, reads the sensor and transmits the payload before going back to sleep.

How then to wake up the processor from deep sleep ? The are several add on options, use a TPL5010 to provide an interrupt pulse to wakeup the processor. Or maybe a TPL5110 to power the whole node on and off. These TPL devices have a very low operating current themselves, under 0.1uA typically.

Or you could use a real time clock (RTC) to provide an alarm wakeup. There are RTCs such as PCF8563 that are low cost and have a very low backup current.

The ATmega328P does have its own very low current internal timer, the watchdog. The maximum time of the watchdog is however only 8 seconds. Thus you can only use the watchdog to sleep the processor in multiple units of 8 seconds. This 8 seconds is not very accurate, but does it really matter if you get a sensor reading every 11 minutes instead of every 10?

A ‘bare bones’ ATmega328 with the watchdog timer active uses around 6uA in deep sleep. For a 15 minute sensor reading interval you need a software loop that counts 112 x 8 second sleeps. Clearly when the processor wakes up there is current used both in a power on time and the time the code is actually active counting those 8 second sleeps. How much power in total this takes is a bit unclear and its not so straightforward to measure.

As part of the development of the SX12XX library;

[https://github.com/StuartsProjects/SX12XX-LoRa](https://github.com/StuartsProjects/SX12XX-LoRa)

I wrote sensor transmitter and receiver programs. The transmitter was based on a SX1278 lora device, standard 3.3V Arduino Pro Minis and BME280 sensor. The receiver was an SX1278 LoRa device and a SSD1306 OLED display. See the \\examples\\SX127X\\Sensor folder in the library for the programs.

Picture

![](/images/Easy_Sensor-1024x723.jpg)

One of the issues with reading remote sensors is that you need to be sure the receiver is picking up valid data from your sensor and not data from some other source. There could well be other LoRa devices in an area and it would not be good for a receiver to get an apparent alarm that was not from one of your own sensors.

To increase reliability the sensor sender program takes a CRC value for the actual sensor data and this is sent out with the sensor data packet. The receiver also carries out additional checks on the incoming data using a simple form of packet addressing. The first byte of the packet indicted the type of packet being sent. The receiver can thus reject any packets that don't match. The sender also sends out a single byte destination address and that must match the address setup for the receiver. Again if the receiver gets a packet that does not have that byte matching it will reject it.

Finally the receiver checks that the CRC of the sensor data matches the CRC sent as part of the packet. That is 8 + 8 + 16 = 32 bits of error checking. So potentially there is maybe only a 1:4294967295 chance of the wrong data being read.

The BME280 sensor used was the 3.3V version which does not have a regulator on it, so the full benefit of the BME280 in deep sleep mode can be achieved.

The ATmega328P was programmed with the mini-core bootloader through the Arduino IDE. I used a 3.3V 'Pro Mini' board of my own design (shown below) which has a MCP1700 regulator so the deep sleep current is circa 2uA. Its easy enough to replicate this approach with other hardware just do a Google search for ‘Bare Bones Arduino’

![](/images/IMG_2662.jpg)

**Pro Miser**

I discharge tested a very small LiPo battery, marked as 150mAhr, on my iCharger 106B and it indicated a capacity around 155mAh.

So how long would this battery power a sensor node, shown below, if the sensor reading was sent out as LoRa at spreading factor 7 bandwidth 125000hz every 15 minutes?

![](/images/IMG_2657-1024x707.jpg)

The sensor was started on 20/12/19 and the battery then reported (remotely of course) as 4218mV. The sensor has now been running for two months with no intervention required and the battery is down to 4068mV.

The battery (only 150mAhr remember) looks like it has a lot of life left yet. But even if it expired tomorrow the average usage would be only 77mAhr a month. A set of 3 AA Alkaline might have a capacity of 2000mAhr, so they would have a life of 2 years+ on this basis.

How long the battery will actually last I do not know, but it does look like the humble ATmega328P is perfectly capable of acting as a long battery life node, with no additional components required to manage sleep mode.

**Update:** April 20th 2020: After 4 months of operation the 155mAhr battery has dropped from 4218mV to 3978mv , so lots of life left yet ............

**Update:** May 20th 2020: After 5 months of operation the 155mAhr battery has dropped from 4218mV to 3928mV. I have been using these small LiPos to check the battery life of a high altitude balloon tracker. I can tell from the reception logs that the battery 3928mV point is reached when the battery is 18% drained (end point assumed to be 3500mV), so based on that this 155mAhr battery may have a life of 27 months. A set of AAA Lithium energizers could therefore last 18 years .............

**Update:** June 20th 2020: After 6 months of operation the 155mAhr battery has dropped from 4218mV to 3898mV. Its dropped only 30mV in the last month.

Stuart Robinson
