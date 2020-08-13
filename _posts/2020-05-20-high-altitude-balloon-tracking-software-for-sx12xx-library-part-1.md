---
layout: post
title: "High Altitude Balloon Tracking Software for SX12XX Library – Part 1"
date: "2020-05-20"
---

One of the objectives for the SX12XX library was that it would be possible to write an application for the library, such as a high altitude balloon (HAB) GPS tracker for example, and that the same Arduino program would run on the SX126X, SX127X and SX128X series of Semtech LoRa modules. These 3 ranges of devices are programmed slightly differently but the SX12XX library presents a common function interface for sketches.

I had written high altitude balloon tracker software in the past so I set about converting this to run on the new SX12XX library. This initial version of the HAB tracker software will transmit the GPS position data using LoRa in a format that is commonly used for balloon tracking in the UK. The data from the tracker, such as time, position and temperature is sent in an ASCII text format such as in this sequence;

PayloadID, SequenceNumber,Time,Lat,Lon,Alt,Satellites,Volts,Temperature,Resets,Status, Errors,TXGPSfixms,Checksum

When transmitted the payload looks typically like this;

$$MyFlight1,29,09:05:19,51.48230,-3.18136,48,7,3970,18,100,0,0,140\*DE98

The separate HAB tracker receiver program extracts the position data from the received payload and displays it on the serial monitor and OLED display (if fitted). The display option allows for a very small portable receiver to be built.

For a balloon high in the sky reception of the LoRa packets over hundreds of kilometres will not be a problem.

The most popular choice for HAB tracker batteries are the AAA or AA Lithium Energizers. These can work down to -40c and have a stable voltage of around 1.5v throughout their life. The AAA Lithium Energizers have a stated capacity of 1250mAhr and a shelf life of 20 years.

![](/images/AAA-Lithiums.jpg)

I decided to test the run time of the tracker using small 100mAhr Lithium Polymer (LiPo) rechargeable batteries, the smaller capacity would make testing more practical.

For the actual tracker transmitter I chose to use one an Arduino Pro Mini board that takes my breadboard friendly modules, this makes for a very quick build of a tracker at low cost. The deep sleep current of this Pro Mini based board around 45uA. There is an ATmega 328P DIP based version of the board that has a deep sleep current of around 2.5uA.

![](/images/Tracker-with-UBLOX.jpg)

The tracker transmitter was set for 434.000Mhz, LoRa spreading factor SF8, bandwidth 62500hz, coding rate 4:5. Power level 10dBm. The payload length was 76 bytes with a LoRa on on air time of 464mS. The GPS was a UBLOX M8N type that is commonly found on market places such as eBay. These GPSs are often used with drone flight controllers such as Pix-hawk. They may have separate wires for connecting an internal magnetic compass but I just ignored these.

The same board build used for the transmitter can be used as the receiver too. The receiver will show the trackers GPS co-ordinates and these are displayed on the serial monitor when attached to a PC or on the OLED display, see the receiver picture below.

![](/images/Receiver-UBLOX-918x1024.jpg)

If the receiver has a GPS connected and that has a valid location fix the software will calculate and display the distance and direction to the remote HAB tracker.

I cycled the 100mAhr LiPo a few times to get a stable capacity, a decent battery charger is a great help here, and set the tracker off running in my garden. I intended to run it until the battery level dropped to 3.5v.

After 348 minutes the battery had dropped to 3.5V. During this test the GPS was powered all the time, so the time reported for how long the program took to get an updated fix was low, around 600mS. This was simply the time taken for the next NMEA position sentences to be read from the GPS. The ATmega328P was not put into sleep mode at any time. The long run time, 348 minutes, was a surprise for the always on GPS mode and would imply that a set of AAA Lithiums would have a life of 72 hours.

### Quectel GPS

The Quectel GPSes, either the L70\\L76 surface mount versions that need an external antenna or the L80\\L86 versions that include a ceramic patch antenna, also have a ‘balloon mode’ which is supposed to work at high altitude, although personally I have been unable to confirm this.

The equivalent UBLOX GPSs would be the MAXM8Q in surface mount and SAM M8Q with ceramic patch antenna. The UBLOX GPSs are not inexpensive but the Quectels are available from various far easy on-line market places at very low cost. These low cost GPSs seem to work well enough for non HAB use, but it remains unclear as to whether they work correctly at high altitudes. In the UK RSComponents would appear to be a reliable source of the Quectel GPSs.

The surface mount GPSs mentioned above do not come with an antenna fitted but for use in Pico high altitude balloons a simple wire antenna is adequate. There are some results of tests using the Quectel GPSs to be found here; **[GPSTutorial](https://github.com/StuartsProjects/GPSTutorial/tree/master/GPS%20performance%20comparisons)**
 
I have developed small PCBs that act as breakouts for the Quectel L70,L76,L80 and L86. The L70 and L76 board can be fitted with a simple wire antenna, which can be adequate for high altitude balloon use or a small ceramic patch antenna, see picture.

![](/images/L70-GPS-Wire-Antenna-2-2-712x1024.jpg)

However soldering the L70/L76 GPS onto the PCB does require good SMT soldering skills.

The L80 and L86 breakout use the larger GPS with the ceramic patch antenna included and this is fairly straightforward to solder to the breakout board. In its simplest form all you need to do is fit the GPS and 4 pin edge connector and solder up two links and your good to go.

![](/images/L80_GPS.jpg)


The Quectel breakout boards can be fitted with components (see the GPS board above on the far right) that will power down the GPS completely and still maintain a voltage on the backup pin which is an unusual feature of a GPS board. In this way the GPSs can be used in hot fix mode (to be described in part 2) where the GPS is powered down between fix updates. In this mode the current the GPS uses drops to circa 8uA. The L80\\L86 board can also be fitted with a CR1220 backup battery on the rear.

The HAB tracker software was amended to provide the option of using the Quectels GPSs, the only change needed to use these GPSes was to change the program line to load the GPS library from;

#### include <UBLOXSerialGPS.h> to #include <QuectelSerialGPS.h>"


The first Quectel test carried out was on the L80 fitted to a breakout board. This GPS weighed 7.87g compared to the 29.28g for the UBLOX GPS described above.

In this test after 303 minutes the battery had dropped to 3.5V.

The Quectel L70 GSP with simple wire antenna was tested next, this lasted 394 minutes by the time the battery had dropped to 3.5V. If the power source was 3xAAA Lithiums, then they might be expected to last 82 hours.

The weight of the parts used for a flight setup of the tracker would be;

Tracker transmitter with SX1276 board, 16.93g

UBLOX M8N GPS 29.28g

Quectel L70 GPS 2.01g

Quectel L80 GPS 7.87g

3 x AAA Lithium Energizers in holder 29.65g

The 100mAhr LiPo, which could power the tracker for 6.6 hours weighed 4.45g

The HAB tracker programs have been added to the \\tracker folder in the SX12XX examples folder.

In part 2, there will be a description of the other modes the tracker can transmit, there is capability for FSK RTTY and a long range location payload intended for ground searches.


