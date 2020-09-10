---
layout: post
title: "Easy Build High Altitude Balloon Tracker"
date: "2016-06-17"
---

Today I am going show you how to build a low cost and low component count LoRa® high altitude balloon tracker.

The tracker is powered by 2 x AAA Lithium batteries which will last in LoRa® only transmission mode for around 25 days, half that with FSK RTTY transmissions enabled.

[Parts-reduced](/images/Parts-reduced-300x214.jpg)

This balloon tracker is suitable for both small Pico (foil party type) balloons and the larger latex balloons. The tracker will send back its location using LoRa® and FSK RTTY. The tracker operates in the UK license exempt 434Mhz region at 10mW power output. Line of sight LoRa® reception ground to balloon will be 250-450km, depending on the altitude of the balloon.

A general introduction to LoRa® tracking is here;

[Long Distance Tracking and Monitoring with LoRa®](https://github.com/StuartsProjects/Test-Reports/blob/master/Long%20Distance%20Tracking%20and%20Monitoring%20with%20LoRa®%20-%20Introduction.doc)

There are not many parts we need for the tracker;

Arduino Pro Mini 3.3v 8Mhz Dorji DRF1278F (or Hope RFM98) 2 x AAA Lithium Batteries Ublox MAX8Q GPS Tracker and GPS PCBs 0.1” and 0.05” pin headers Ernie Ball Custom Gauge 13 Guitar String Bits of wire

FSK RTTY would normally be received on a SDR type radio dongle or other HAM radio set-up. The LoRa® receiver is a much simpler set-up and can be just another tracker transmitter board or a low cost ATMEGA2560 base and DRF1278F\\RFM98 shield.

The tracker and receiver software implement a command and control interface so that most all of the tracker settings, frequency, LoRa® settings, RTTY, frequency of transmissions, power saving etc, can be changed remotely whilst in flight.

The software uses a few internal processor tricks to read the battery voltage and temperature without needing any additional components. You can however fit a BME280 or BMP280 I2C sensor if you wish. The design is an improvement on a previous flight tested version.

This tracker is being built for a Pico balloon and as we are going to power it direct from 2 x AAA Lithium batteries (3V), we will need to remove the regulator from the Pro Mini. The two LEDs on the Pro Mini will be also be removed as they just waste power.

The easiest way to remove the regulator and not damage the board is to use a very sharp scalpel to cut through the regulator leads at the point they join the regulator body.

![Remove Regulator - Reduced](/images/Remove-Regulator-Reduced-300x181.jpg)

Removing the LEDs can be tricky, so its easier to remove the series resistors for the LEDs instead. This version of Pro Mini also has a resistor feedback network for the regulator across VCC, these consume power so should be removed. Just push the resistors aside with a soldering iron. The picture shows the Pro Mini with unwanted parts removed, locations of the removed components are circled in red.

![Removed Components](/images/Removed-Components-300x173.jpg)

We should reprogram the brown out level for the processor, as default the extended fuse is set to 0x05 on the above Pro Minis giving a brown out level detect of 2.7V. This is OK if your using the 3.3V regulator, but a little close for a processor running at 3V. I used AVRDUDESS with a USBTiny programmer to reset the fuse to 0x06, giving a 1.8V brownout level. There are plenty of tutorials on the Internet on how to re-program the boot loaders on Arduinos and you can in this re-programming set the extended fuse to 0x06.

Next solder the PIN headers in place. The 0.1” pin headers are for the Pro Mini, the 0.05” ones for the DRF1278F. Use the respective module to hold the pin headers in place whilst soldering. DO NOT solder the modules themselves  in place yet. Note the 2 way pin header for the A4 and A5 connections. The Pro Mini fits on the bottom of the PCB, the DRF1278F on the top. The top side has the text printing on it.

![Pro Mini Headers 1 - Reduced](/images/Pro-Mini-Headers-1-Reduced-300x173.jpg)

![Pro Mini Headers 2 - Reduced](/images/Pro-Mini-Headers-2-Reduced-300x164.jpg)

![0_1 inch Pin headers - reduced](/images/0_1-inch-Pin-headers-reduced-300x144.jpg)

Solder an angled header to the programming port on the Pro Mini. Coat the underside of the Pro Mini and DRF1278F with some conformal coating to protect against moisture. Solder the Pro Mini in place, add the wire link (white) shown, and add the battery wires. Solder the DRF1278F in place.

![Pro Mini and Battery Lead - Reduced](/images/Pro-Mini-and-Battery-Lead-Reduced-300x187.jpg)

![Wire Link - Reduced](/images/Wire-Link-Reduced-300x152.jpg)

 
Solder the Ublox GPS onto the PCB and fit the GPS PCB to the tracker PCB with pin headers. If you don’t want to assemble the GPS module yourself, the tracker PCB will also accept a GPS breakout PCB sold by Uputronics in the UK.![Connect GPS - Reduced](/images/Connect-GPS-Reduced-300x195.jpg)

Cut two lengths of guitar wire 9.5cm long (for ¼ wave radials) and one 14.25cm length for the ¾ wave vertical. Push the 9.5cm wires halfway through the two holes provided either side of the antenna and solder in place. Solder the longer wire to the antenna connections. See photo; radials are on the left, 3/4wave vertical on the right.

![GPS Antenna - Reduce](/images/GPS-Antenna-Reduce-300x286.jpg)

You can fit a JTI 1575AT43A40 Chip Antenna instead of the Guitar wire, but signal levels to the GPS will be significantly lower, this has the effect of increasing current consumption.

Solder a 17.3cm bit of guitar wire in place for the DRF1278F antenna, this is a ¼ wave at 434Mhz. Before the tracker is actually launched, cut two bits of guitar wire 34.6cm long and fit these as radials in the holes provided either side of the centre pin of the antenna connector.

![Module Antenna - Reduced](/images/Module-Antenna-Reduced-300x228.jpg)

Connect a CH340G USB programming adapter, which must be set to 3.3V mode and your ready to go. Do not connect a battery and the programming adapter at the same time and never power up the tracker without an antenna connected.

![Program Adapter - Reduced](/images/Program-Adapter-Reduced-300x262.jpg)

For the receiver I have been using a Arduino ATMEGA2560 3.3V/5V base set to 3.3V mode and a RFM98 shield PCB. From the picture you can see that it needs very little assembly. The shield board will work with 3.3V Arduino UNO bases as well.

The red and black lead is the AFSK upload lead. On receipt of the LoRa® payload the receiver software sends the payload as AFSK RTTY at 1200baud. This output can be connected to a PC or Pi running the HAB version of FLDIGI via the sound card. The received audio will be decoded by FLDIGI and sent via the Internet to Spacenear for tracking.

![ATMEGA Base - Reduced](/images/ATMEGA-Base-Reduced-229x300.jpg)

For a more compact and slightly lighter tracker you can replace the pin headers with short bits of wire, spacing the Pro Mini and DRF1278 modules away from the PCB with a thin bit of card whilst you solder the wires in place.

The tracker PCB can also be used as a receiver by fitting a SMA edge connector and adding the components that for the AFSK upload into FLDIGI, see below. The receiver would be powered via the CH340G programming adapter.

![DRF1278F Receiver](/images/DRF1278F-Receiver-300x151.jpg)

There is space on the PCB for fitting additional components, including extra decoupling capacitors on both tracker and GPS PCBs.

A  serial GPS can be fitted if you want to use the tracker with a larger HAB, fit a 4 pin header in the location outlined in white on the PCB.

There is an equivalent PCB for the RFM98, that needs 2mm pin headers.
