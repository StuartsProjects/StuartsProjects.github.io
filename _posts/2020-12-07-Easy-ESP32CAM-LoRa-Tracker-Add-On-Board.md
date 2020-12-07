---
layout: post
title: "Easy ESP32CAM LoRa Tracker Add On Board"
---


OK, I will admit it, the previous version of this board has a fault. Since the GPS shares the same serial port that is used for program upload, the GPS could not be connected at the same time as program upload. The intention was that a swithc would be use to disconnect the GPS TX pin during programming. 

If the tracker was to be fitted with one of my lightweight GPS tracker boards;

[**Breakout Board Quectel L70 and L76 GPS**](https://www.tindie.com/products/20921/)

**[UBLOX MAX 8MQ GPS Breakout Board](https://www.tindie.com/products/21276/)**

The the GPS would need to be permanently attached. 

So I revised the board and at the same time made it easier to build by removing the need to fit surface mount devices. Even the RFM98 LoRa device can be fitted in place with 2mm spacing pin headers if you dont fancy soldering SMD.

I first built up the board with sockets to test everything was working OK. The add and board could be removed from the ESP32CAM and the RFM98 was on sockets too. I fitted pin headers for the programming and debug connection, although in the built up tracker these would not be needed, test clips can be used instead, thus saving weight.  

<br>
<p align="center">
  <img width="460"  src="/images/Easy_ESP32CAM_Tracker_Test_Assembly.JPG">
</p>

<br>


That all appeared to be OK, so I assembled the add on board ready for adding to the ESP32CAM. If you want to use the debug feature you need to attach the wire link (red) shown in the picture. 


<br>
<p align="center">
  <img width="460"  src="/images/Easy_ESP32CAM_debug_wire.JPG">
</p>

<br> 

The regulator fitted on the ESP32CAM is an AMS1117, which is not ideal for a low weight tracker as it needs around 4.6V of input to regulate properly. That would require 4 x AAA batteries or two lithium batteries in series to power it. Not ideal or light. The TrackerAddOn was arranged so that the ESP32CAMs AMS1117 could be removed and an MCP1700 or HCT7833 regulator fitted on the TrackerAddOn would replace it, choice of SOT89 or T092 pin devices. These are true low drop-out voltage regulators and operation from a single lithium battery would now be possible. 

The AMS1117 was removed and a TO92 HCT7833 regulator was fitted on the add on board, this is optional, leave the AMS1117 in place if you have high enough voltage batteries.   

To the assembled board was added a L70 GPS breakout board and the programming and debug port connections made using test hooks. You can fit pin adders if you want, but it does add a little weight. 


<br>
<p align="center">
  <img width="460"  src="/images/Easy_ESP32CAM_Tracker_Programming.JPG">
</p>

<br> 


The assembled tracker ready to go weighs circa 13g, plus the weight of the battery chosen. 


<br>
<p align="center">
  <img width="460"  src="/images/Easy_ESP32CAM_Tracker_Profile.JPG">
</p>

<br> 



Holes are positioned in the TrackerAddOn board so that when it was soldered in place you could use a rod or drill to access the reset switch and another hole allowed you to see the pin 33 led so it could be used as an indicator. A pad was added so that you could solder a wire to pin 33 of the ESP32 and connect it to the pad on the TrackerAddOn. This then allowed for an external connector for pin 33 so that it could then be used as a serial debug port. 

The software for the ESP32CAM tracker software will be added to the SX12XX library in due course.  
<br>
<br>
#### **Stuart Robinson**
#### **December 2020**     