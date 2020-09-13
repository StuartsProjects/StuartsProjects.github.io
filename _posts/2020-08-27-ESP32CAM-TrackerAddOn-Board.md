---
layout: post
title: "ESP32CAM-TrackerAddOn-Board"
---

<img align="right" src="/images/TrackerAddOn.jpg" width="350">


With the basic ESP32CAM GPS and camera tracker code working with a RFM98 LoRa® device, I designed a small add on board that would allow the ESP32CAM to be used as a small battery powered tracker. 

The idea for the board was that that you could solder it piggy back style direct onto the ESP32CAM pins. The board would have a LoRa® device with  connections for an antenna, programming, GPS, battery and a serial debug output. A switch for boot mode was also needed. 

The regulator fitted on the ESP32CAM is an AMS1117, which is not ideal for a low weight tracker as it needs around 4.6V of input to regulate properly. That would require 4 x AAA batteries or two lithium batteries in series to power it. Not ideal or light. The TrackerAddOn was arranged so that the ESP32CAMs AMS1117 could be removed and an MCP1700 or HCT7833 regulator fitted on the TrackerAddOn would replace it. These are true low drop-out voltage regulators and operation from a single lithium battery would now be possible. 

Holes were positioned in the TrackerAddOn board so that when it was soldered in place you could use a rod to access the reset switch and another hole allowed you to see the pin 33 led so it could be used as an indicator. A pad was added so that you could solder a wire to pin 33 of the ESP32 and connect it to the pad on the TrackerAddOn. This then allowed for an external connector for pin 33 so that it could then be used as a serial debug port. 

A switch was incorporated to allow the GPS to be disconnected from circuit (its on the programming port pins) so that you could leave the GPS connected whilst programming. Unfortunately I got the wiring for this wrong, but apart from that the board works as intended. 

The ESP32CAM comes in two apparent versions, one with a thick PCB weighing 7.8g and a thin PCB version weighing 7.2g. A Ublox Max8Q GPS on a breakout PCB with a wire antenna weighed in at 1.62g and the add on PCB with LoRa® module and wire antenna weighed 4g. So we have a LoRa® based tracker with camera, SD card and GPS that weighs 13.4g plus battery. The picture of the assembled tracker shows a L70 GPS with a small ceramic antenna fitted, that GPS weighed 7.9g.


<img align="right" src="/images/ESP32CAM_Tracker_2.jpg" width="350">



### Power consumption

With the AMS1117 on the ESP32CAM board removed and a HCT7833 fitted to the TrackerAddOn board, the deep sleep current was 240uA with no camera or microSD card fitted. Adding the camera increased deep sleep current to 510uA and adding a Kingston microSD card took the deep sleep current up to 1.14mA.

Adding the Quectel L70 GPS took the sleep current up to approx 20mA, which is not a surprise with the GPS running all the time. The Quectel L70 GPS can be put into software backup mode when it has a fix. When you tell the GPS to wake up it can then get a fix within a few seconds. If your not transmitting on a continuous basis but only say once a minute there is the potential to save a good deal of power. 

With the GPS put into backup mode the GPS current reduced from around 20mA to 0.6mA. 

The ESP32CAM tracker software was loaded and with the camera, SD, GPS and LoRa® device in a working set-up the sleep current was just under 2mA. 

Whilst the 2mA sleep current is not low enough for very extended tracker operation over weeks there is the potential to use this humble low cost TrackerAddOn board and ES32CAM for a lot of tracking applications, even if you dont use the camera or SD.  


### What about the battery ?

Or more importantly how long does a battery last ?

One of the easiest ways to check power consumption of GPS trackers is to use a low capacity battery and see how long it lasts, in this case a 100mAhr Lithium polymer was used.

I expected this small capacity battery should run down fairly quickly, the ESP32 is not a low running current micro controller. With the battery fully charged you I ran the real working software to see how long the battery lasts. As long as you dont abuse these small batteries and charge them properly, the capacity wont change much between uses so you can make changes or potential optimisations to the tracker software or hardware and directly measure the effect on battery life. 
 
The tracker put in the middle of my garden and left to run. Even with a simple wire antenna the GPS got a fix in 42 seconds. The tracker then started taking pictures and transmitting its GPS location ever 60 seconds. The GPS was initially left running all the time so the current consumed between pictures and in sleep mode was around 20mA. The tracker was stopped when the battery had fallen to 3.5v. In this test the 100maHr batter lasted **162 minutes**.

Now 162 minutes from a 4.6g battery, giving a tracker weight of 18g, is not bad at all, better than expected really, especially as the ESP32 was running at 240Mhz. 

For the next test I set the ESP32 to run at 80Mhz and decided to see how long it would be before the tracker actually stopped transmitting. This time the battery lasted **296 minutes**, although the SD card had stopped responding 20 minutes earlier. So almost 5 hours of operation from a tiny 100mAhr battery. 

### GPSs in software backup mode.

What would happen if we now use the GPSs in software backup mode ? In this mode the GPS retains the satellite information so that when powered up again it should quickly get a new fix and you can then power it back down again. The GPS does not now spend all the time running on full current. With this change made the GPS was getting a fix on power up sometimes within 2 seconds and sometimes a bit longer. The tracker run time increased to **584 minutes**, almost 10 hours, which from a 100maHr battery is almost good.

The software for the ESP32CAM tracker software will be added to the SX12XX library in due course, but first I need to check battery life when the tracker is sending out the HAB payload as FSKRTTY as well as the long range search LoRa® packet. 
<br>
<br>
#### **Stuart Robinson**
#### **August 2020**     