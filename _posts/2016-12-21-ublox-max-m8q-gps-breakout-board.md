---
layout: post
title: "UBLOX MAX M8Q GPS Breakout Board"
date: "2016-12-21"
---

![GPS Breakout Finished](/images/GPS-Breakout-Finished_thumb.jpg "GPS Breakout Finished")


For those that want to build their own GPS Breakout board, I sell a PCB that allows you to do just that. Primarily designed for my own HAB tracker boards it can be used for other trackers and has both the Serial and I2C interfaces on the 8 way pin header. A previous article described how to attach this GPS board to my tracker boards.

For HAB tracking you can miss out most of the components. You if you wish can fit a small Lithium battery with some DS sticky tape and the components to trickle charge it from the trackers supply or fit an LED for the time pulse pin. My own tracker boards can have the components fitted to turn off the power supply to the GPS, whereby the battery will backup the GPS and allow it to be used in hot fix mode.

On the rear of the PCB are the pads for a small inductor, this helps to protect the GPS antenna input against static damage, a problem to which the UBLOX GPS are very prone to. The inductor can be omitted but take great care when handling the assembled break out board. If your UBLOX GPS takes a long time to get a fix, or never does, a probable cause is static damage to the antenna pin. The GPS data sheet does mention this as an issue.

You can fit the JTI\_ANTENNA-1575AT43A40 ceramic stick antenna, but the board can also be used used with a 1/4 wave or 3/4 wave wire antenna and 1/4 wave radials. This will significantly improve GPS reception over the ceramic antenna, which in turn can significantly reduce power consumption, especially if hot fix mode is in use. For these latest GPS breakout boards you will need to use Ernie Ball Custom Gauge 9 wire. 

![Inductor on PCB](/images/Inductor-on-PCB_thumb.jpg "Inductor on PCB")

Taking suitable antistatic precautions, fit the inductor to the rear of the PCB

![Ceramic antenna on PCB](/images/Ceramic-antenna-on-PCB_thumb.jpg "Ceramic antenna on PCB")

If your fitting the ceramic antenna add it now.

Now position the GPS carefully in place and solder all the pads. I added C1 for some extra on board decoupling.

Add the 0.1” pin headers as shown in the first picture or solder the breakout PCB direct to one of my tracker boards and your ready to go.
