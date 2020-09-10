---
layout: post
title: "ESP32 IOT node power saving"
date: "2020-05-28"
---

The ESP32 is often used for IOT Arduino projects, it is very low cost with plenty of memory, built in Wi-Fi and Bluetooth.

A standard for low power IOT nodes could be considered as the ATmega328P as used on Arduino UNOs and Pro Minis. In the ‘bare bones’ format, where a processor and very low current regulator is used, the ATMega328P can in deep sleep will consume around 2uA of current. If you want to put the IOT node to sleep for long periods, say 15minutes, then the ATMega328P processor watchdog can be used for the wakeup and it only increases deep sleep current to circa 6uA.

The ESP32 has a deep sleep mode also, although it does result in a wakeup that is a restart\\reset of the processor. The ATmega328P can wakeup from sleep and resume its program from just after where it went to sleep. This type of program resume can save a fair bit of running time (and hence power) as the program does not need to go through a boot loader delay or initial device configuration.

Its also the case that the ESP32 typically runs at a much higher speed than the ATmega328, 240Mhz versus 8Mhz. The higher speed of the ESP32 results in a very much higher running current for the processor, 62mA versus 4mA for the ATMega328P. The ESP32 can however be set to run at as low as 10Mhz CPU frequency.

So what is the real world difference in an IOT application of using the ESP32 versus the ATmega328P?

I wrote a test program that would on wake up from sleep, read a sensor (a voltage) increment a couple of counters, save the results in non-volatile memory, then transmit a LoRa® packet of 32 bytes at bandwidth 125000hz, spreading factor 12 and 14dBm transmit power. The on-air time was 1800mS. This would be typical of a node on the Things Network (TTN) in long range mode. There was a 15 second sleep, so the packet was being transmitted around every 18 seconds.

I used a USB power meter to monitor how many mAhr was being used. Its unclear how accurate the USB power meter would be in a situation where there could be sharp peaks in current consumption, but the test should give a valid comparison between nodes in different setups. The setup is below, A 3S Lithium battery as the power source with a step down converter supplying 5v to the USB power meter which is measuring the current used by the ESP32 IOT node.

![](/images/ESP32-Node-under-test-1024x692.jpg)

ESP32 IOT node test setup

The ATMega bare bones style node with a CPU frequency of 8Mhz used 35.7mAhr of power to transmit 1000 payloads or packets.

Next up was the ESP32 running at its default of 240Mhz using the deep sleep mode, this used 88mAhr for 1000 packets.

We don't really need to run the ESP32 at 240Mhz for our simple IOT node, so the program was reloaded to run at 20Mhz. This time the power for 1000 packets was 45.3mA.

So the ESP32 used some 27% more power than the ATMega to achieve the same task. This is not a major difference and does show that the ESP32 is practical for low power IOT nodes, you don't have to use it at 240Mhz.

The ESP32 does have a light sleep mode which does allow a program to continue on wakeup just after the point it went to sleep, just like the Atmega328P deep sleep mode. The power used for the node using light sleep mode was 38.2mAhr

## Summary

#### Power consumption of IOT node, sending 1000 packets

ESP32 @ 240Mhz CPU speed using deep sleep 88mAhr

ESP32 @ 20Mhz CPU speed using deep sleep 45.3mAhr

ESP32 @ 20Mhz CPU speed using light sleep 38.2mAhr

ATMega328P @ 8Mhz CPU speed using deep sleep 35.7mAhr

#### Idle currents versus CPU speed

ESP32 @ 240Mhz CPU speed idle current 62.6mA

ESP32 @ 160Mhz CPU speed idle current 41.8mA

ESP32 @ 80Mhz CPU speed idle current 29.3mA

ESP32 @ 40Mhz CPU speed idle current 16.2mA

ESP32 @ 20Mhz CPU speed idle current 11.9mA

ESP32 @ 10Mhz CPU speed idle current 9.7mA

ATMega328P @ 8MhzCPU speed idle current 4mA
