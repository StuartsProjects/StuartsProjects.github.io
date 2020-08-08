---
layout: post
title: "Building the Relay Board"
date: "2016-08-15"
---

**LoRa Relay – Part 2**

A LoRa relay is a powerful tool in the search for a tracker. The concept is simple, the relay is put in an elevated position and listens for and re-transmits the LoRa packets.

Height above ground, particularly in relatively flat areas, can significantly improve signal reception and range. The relay PCB is small and light, taped to the end of a 10M telescopic pole it can improve signal reception by 6-10dB, this represents a double or more increase in range.

The relay board can be built to take a battery charger, so you can put the relay in a waterproof box and keep the battery charged with a small solar panel, current consumption of the relay is around 15mA average.

![1](/images/1_thumb.jpg "1")
 

Separate the PCB by scouring the line of small holes in the PCB centre with a sharp strong blade, and snap the PCB in two.

 

First solder the components that are fitted to the bottom side of the PCB, these will be out of sight underneath the Pro Mini when assembly is finished. These components are R1,R2,R3,R4,D1,D4, see picture below.

![2](/images/2_thumb.jpg "2")
 

There are two 5pin angled headers to fit. The one on the top of the PCB (the side with the DRF1278F and the PCB lettering) fits in the row of holes nearest the non-antenna end of the PCB. This header is for the connection of the battery and another supply such as 5V from a RC receiver. See the picture right below with the top 5 pin header and the Polyfuse fitted.

 

The header on the bottom of the PCB is for a I2C or serial GPS connection. Once the Pro Mini is fitted in place, there is enough of a gap for a standard 0.1” header plug to be connected. Solder the 0.1” pin headers for the Pro Mini on the bottom of the PCB, note the 2 pins for the A4 and A5 connection (left picture below) Solder the bottom 5 pin angled header in place as well. Cut the surplus off the pins as flush as you can to the PCB surface.

![5](/images/5_thumb.jpg "5")

![4](/images/4_thumb.jpg "4")


Fit the 0.05” pin headers for the DRF1278F (right picture above).  These are a tight fit, you may need to sand a chamfer on the inside edge of the headers plastic separator, this part does get very close to the solder joints for the Pro Headers 0.1” pins. Don’t forget the 2 way pin header for the DRF1278F antenna connection.

Fit an angled 5 pin header to the Pro Mini itself, or for a smaller relay, build a test clip adapter and use test clips to attach to the program connections. Load the test program into the Pro Mini, and check that the pin 13 LED flashes as described for that program. You dont want to find that you cannot program the Pro Mini once its soldered in place.

Fit a 17.5CM length of wire for the antenna, assuming the frequency is 434Mhz or close to that. Solder the Pro Mini in place on the PCB bottom, then the LED on the top, note the orientation of the rounded part of the LED body . Run the test program again, see that the LED just fitted flashes as expected. 

![7](/images/7_thumb.jpg "7")

![6](/images/6_thumb-1.jpg "6")
 

Solder the DRF1278F in place on the PCB top. Run the test program again and you should hear the FM tone on a UHF hand held.

With the relay assembly finished load the relay software and place it near a tracker transmitter that is working, and has the same LoRa parameters and frequency.

Your receiver should now pick up the trackers original transmission, then after a short delay the same packet re-transmitted by the relay. The relay may then also report the link SNR of the packet received, if that option is enabled in the program.

The Relay board can function as a tracker in it own right, see pictures below;

![8](/images/8_thumb.jpg "8")


**Next: How to get the relay up a  tree.**
