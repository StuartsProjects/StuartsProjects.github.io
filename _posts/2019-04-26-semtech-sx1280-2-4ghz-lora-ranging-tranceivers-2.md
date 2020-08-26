---
layout: post
title: "Semtech SX1280 2.4Ghz LoRa Ranging Tranceivers"
date: "2019-04-26"
---

The Semtech SX1280 LoRa devices bring long range LoRa technology to the 2.4ghz bands. The devices allow for much higher data throughput than the lower frequency LoRa devices, data transfer speeds are up to 203kbps.

The SX1280 also has a ranging mode that can measure distance by recording the time of flight of a special packet exchange between two SX1280s.

I have designed some plug in boards for the Ebyte and G-NiceRF SX1280 LoRa devices. The larger green PCBs are an ATMega328P based Mini Logger I also designed. It can be fitted with a single breadboard friendly plug in board, see the picture below;

![](/images/Arduinos2-1024x993.jpg)

The PCBs shown, the yellow ones, are breadboard friendly so will plug into a standard 0.1" breadboard, very useful for prototyping.

A complete Arduino based transceiver fitted with an G-NiceRF SX1280 device is shown on the left of the picture above, complete with 2dBi WiFi antenna. The board was programmed in the Arduino IDE.

Semtech publish sample code for the SX1280 but its not directly suitable for the Arduino environment so I used the Semtech code as a basis for writing some Arduino library files.

## **First Ranging tests**

I used a large open field to test the ranging, these were the results;.

	Actual Distance   Indicated Distance.
	0                 4.4M 
	50M               57.6M 
	100M              103M 
	150M              148M 
	200M              201M 
	250M              253M

So not too bad, the results did seem to be consistent for large open areas. So short distance ranging seems OK, what about longer distances ?

## **4.4km Ranging test**

North of the Cardiff where I live there is a ridge and the location is around 150M higher than where my shed is located in the city. With a SX1280 transceiver on top of the 6M mast attached to my shed there is a relatively clear line of sight from the ridge to the device on the mast, the distance is 4.4km.

![](/images/word-image-9-1024x768.jpeg)

I wrote ranging software that would first transmit a ranging request at high power (10dBm!) and report whether the ranging request completed. The transmit power was then reduced and another ranging request made.

Over the 4.4km link the ranging worked down to -14dBm. Thus if the full power was used, 10dBm, this test indicates the ranging may have potential to work at over 69km.

## 40km Ranging Test

There is a location North of Cardiff (altitude 264m) suitable for a 40km link test to Black Down (altitude 325m) in the Mendips, the other side of the Bristol channel.

![](/images/word-image-10-300x199.jpeg)

At the Cardiff end there was a SX1280 ranging receiver fixed to a pole and another for a basic link test. The view from here towards the Mendips is below;

![](/images/word-image-11-300x225.jpeg)

At the Mendips end I had a matching set of devices, one as the ranging test instigator/transmitter and another for receiving basic link test packets. The results were logged to a micro SD card for later analysis. Basic 2dBi antennas were used at both ends.

At the Mendips end the ranging request transmitter would emit a long beep at the start of the test sequence, send a ranging request at 10dBm, then 9dBm, 8dBm etc. If the ranging request was successful then the would be a short beep. Thus to work out the power level when the ranging stopped working you only had to listen for the long beep and then count the short beeps.

The LoRa settings for the ranging were SF10 and bandwidth 406khz, the longest range settings that the SX1280 allows for ranging mode.

## **Ranging calculations**

The path used for the '4.4km Ranging test' mentioned above was used to test and calibrate the ranging function. The ground distance was 4.42km as measured on Google maps. The average ranging result from the SX1280 was 24515. This gave a conversion factor to metres of 0.1803.

The average ranging result for the Cardiff to Mendips link was 225982. Applying the 0.1803 conversion factor gives a distance of **40.745km**. The distance taken from Google Maps was **40.650km**. So the SX1280 ranging produced a measurement of +0.2% over actual. Not bad.

## **Summary - Distance Potential of the SX1280 ranging and point to point.**

The ranging requests were received down to 4dBm over the 40km link. This would imply a potential range of **80km** LOS at 10dBm. Even modestly improved antennas at both ends (see antenna comparisons above) could double this range.

The longest range settings for point to point mode, SF12 and bandwidth 203khz should have a link advantage of 8dBm over the ranging settings described above, SF10 and bandwidth 406khz. Converting this 8dBm link advantage over the ranging settings, suggests a potential range for point to point LoRa in the **200km** region.

## **SX1280 Program Code**

Semtech publish source code in C++ and a development kit for the SX1280 based on ARM\\MBED. I have adapted the code to work under the Arduino IDE, its been tested on ATMega328P and ATMega1284P. There is still some work to do on the ranging part of this code, when it it ready it is my intention to publish it as an Arduino library. 

All the parts used in these tests were self funded, no complimentary parts were used.
