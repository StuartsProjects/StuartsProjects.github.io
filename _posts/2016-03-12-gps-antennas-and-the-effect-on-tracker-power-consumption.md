---
layout: post
title: "GPS Antennas and the effect on Tracker power consumption"
date: "2016-03-12"
---

For some time I have wondered if you could improve the battery life of PICO High Altitude balloons tracker if you used the GPS's hot fix capability.

The common GPS for PICO HABing is the UBLOX MAX8 and if that is given a battery backup supply the GPS will save the down loaded satellite data in battery backed up memory when the GPS is powered off. It should then quickly acquire a new fix when its powered back on. The potential for power saving is clear, if you only want a new GPS fix every minute or so, why have the GPS powered up and running all the time, even if its in power save mode ?

The first thing to check is the effectiveness of the various antenna set-ups. For this I used the UBLOX U-Centre application which will display the signal strengths for each of the satellites the GPS is picking up signals for.

The GPS and tracker were position on a mast approximately roof top level in my garden, it's fairly flat where I live so the GPS antenna would have a reasonable view out to the horizon.

Below are screen shots of the reported signal strengths in dB-Hz of the received satellites for the antennas tested. For comparison the signal reports for two GPS modules with integral ceramic patch antennas are included.

![JTI 1575AT43A40 Ceramic Chip Antenna.](/images/GPS1-265x300.jpg)

JTI 1575AT43A40 Ceramic Chip Antenna

![DIY 1/4 Wave vertical wire and 4 x 1/4 wave radials.](/images/GPS2-264x300.jpg)

 
DIY 1/4 Wave vertical wire and 4 x 1/4 wave radials
 
![DIY 3/4 Wave vertical wire and 4 x 1/4 wave radials.](/images/GPS3-263x300.jpg)

DIY 3/4 Wave vertical wire and with 1/4 wave radials

![PA6H Small Ceramic Patch, 15mm x 15mm](/images/GPS4-267x300.jpg)

PA6H Small Ceramic Patch, 15mm x 15mm

![UBLOX 7 Large Ceramic Patch, 24mm x 24mm.](/images/GPS5-265x300.jpg)

UBLOX 7 Large Ceramic Patch, 24mm x 24mm

I set up one of my GPS PICO trackers to power the GPS up, measure how long it takes to get a new fix, report the fix time and then turn off the power again. I set the software to power up the GPS for a fix approximately once a minute.

Its clear from the signal strength screen shots shown earlier that the DIY wire antenna is far more effective than the common ceramic chip antenna, so how does the difference affect the time take for a hot fix ?

**JTI 1575AT43A40 Ceramic Chip Antenna**.

First I ran the UBLOX GPS with the small ceramic antenna most often used for a PICO HAB.

The Hot fix times over a 15 minute period were;

24964mS, 36959mS, 13959mS, 31961mS, 16945mS, 34978mS, 18982mS, 59996mS, 31975mS, 51994mS, 80005mS, 25969mS.

That is an average of 35.72 seconds per fix. Hardly worth the bother of using hot fix, as the GPS is in acquisition mode during hot fix you may well use more power than a GPS left running in cyclic power saving mode.

**DIY 1/4 Wave vertical wire and 4 x 1/4 wave radials.**

Constructing the antenna requires that you have holes to fit the lightweight wire and ground plane radials, but it is easy to build these into a PCB design. The wire used is Ernie Ball Custom Gauge 13. Easy to solder, be careful to bend the sharp cut ends over to avoid stabbing injuries.

The Hot fix times over a 15minute period were;

7259mS, 7768mS, 7253mS, 7254mS, 7281mS, 7280mS, 7285mS, 7286mS, 7282mS, 6777mS, 7288mS, 7286mS, 7271mS, 7283mS.

An average of 7.27 seconds per fix.

**DIY 3/4 Wave vertical wire and 4 x 1/4 wave radials**.

The Hot fix times over a 15minute period were;

1954mS, 977mS, 1945mS, 1958mS, 968mS, 1953mS, 1962mS, 1958mS, 1951mS, 955mS, 1950mS, 1944mS, 1915mS, 1940mS.

An average of 1.74 seconds per fix.

I sent the results to Michael Kirkhart, a colleague on the $50SAT project, he is a design engineer working for NeverLost GPS systems, he said;

_“Based on the C/N0 numbers you captured, I am not surprised by the long hot fix times you reported for the ceramic antenna. The 3 pieces of information the GPS receiver needs to quickly produce an initial position solution (assuming, it is able to quickly acquire and track at least 4 or more satellites) is the approximate position of the receiver, the ephemerides and clock correction for all tracked satellites, and time. Under hot start conditions, how quickly it can re-synchronize with GPS time is what will drive the time to first fix (TTFF, in GPS jargon). To do this, it has to be able to achieve sub-frame sync with the navigation message from at least one of the satellites. The C/N0 threshold for reliable decoding of the navigation message is about 26 dB-Hz, at least for the SiRF GPS receivers I have worked with. It appears the uBlox receivers have about the same threshold. Since the strongest signal from the ceramic antenna is about 25 dB-Hz, it is below the threshold, and the probability of bit decode errors increases dramatically. Under these conditions, the receiver has no choice but to try again and again until it can perform a reliable decode. This can take a long time, as you have shown”_

So what does this mean in practice ?

Although the GPS in hot fix mode consumes around 20-25mA, if its only on for 2-3 seconds a minute, then average GPS current consumption is down to around 1mA, much lower (on average) than if the GPS was kept on and in cyclic power saving mode. You also now have a choice. For some flights a GPS update may not be required every minute so you can choose to power up the GPS less frequently, resulting in even lower average current consumption.
