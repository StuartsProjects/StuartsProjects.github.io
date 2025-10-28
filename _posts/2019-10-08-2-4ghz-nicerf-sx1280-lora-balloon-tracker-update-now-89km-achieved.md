---
layout: post
title: "2.4Ghz NiceRF SX1280 LoRa® Balloon Tracker – Update - Now 89km Achieved !"
---

Although I have launched a few pico, foil party type balloons, I had not participated in the launch of a ‘real’ high altitude balloon (HAB) the latex type that rises to 30km or so then bursts and comes back to ground.

There was a HAB workshop planned for 25th/26th July in Braunton Devon so I signed up.

Bill, who was running the workshop agreed to give one of my new 2.4Ghz LoRa® trackers a ride up to altitude. The tracker was a simple design using an ATmega328P processor used with my own Arduino library for the Semtech SX1280 LoRa® device.

![](/images/word-image-3.jpeg)

The tracker board itself (left in picture above) is not specifically designed as a radio frequency (RF) tracker and does not have connections for directly mounting an RF device. 

I make my own breadboard friendly boards for the Hope RFM9x, DRF127x, RN2483 LoRa® devices and most recently one for the NiceRF SX1280 LoRa® devices. The simple Arduino board can then be used for 434Mhz, 868Mhz or 2.4Ghz LoRa® devices just by using the appropriate board. The boards can either plug in on sockets, good for testing or last minute changes, or be soldered permanently in place for a more compact tracker. The components needed are all wired through hole types, so the board is very quick and easy to assemble.

![](/images/word-image-4-1024x865.jpeg)

The assembled 2.4Ghz tracker is fairly compact and I choose to use a standard small Wi-Fi antenna connected to the RP SMA socket.

![](/images/word-image-5.jpeg)

The assembled tracker has pin headers for connection of a GPS and\\or sensors such as BME280 I2C types.

The PCB files for a small Arduino Pro Mini based controller that can be used with the NiceRF SX1280 boards are available here;

[**Pro Mini based controller device repository**](https://github.com/StuartsProjects/Devices/tree/master/Easy%20Pro%20Mini)

And the PCB files for the plug in board for the NiceRF SX1280 module are available here;

[**NiceRF SX1280 module device repository**](https://github.com/StuartsProjects/Devices/tree/master/Breadboard%20Friendly%20NiceRF%20SX1280%20Board)

The board can be used with just two AAA Lithium energisers which will supply around 2.9V for most of the batteries life. The basic board has a very low quiescent current regulator so can be used with 3 x AA batteries and still keep the sleep current of the board below 2uA.

The tracker was fitted into the HAB payload box, shown below at top left with the small L70 GPS at top right. The antenna poked out of the top of the box which was not ideal but

the Raspberry Pi in the Sky (PITS) tracker had two antennas on the bottom, one for FSK RTTY and another for LoRa®.

![](/images/word-image-6-1024x768.jpeg)

This is the balloon being filled with helium at the launch site.

![](/images/word-image-7-1024x768.jpeg)

And the balloon shortly after launch with the payload below.

![](/images/word-image-8-768x1024.jpeg)

My tracker setup was fairly simple, a low cost Wi-Fi yagi on a tripod and one of my LCD receivers with a SX1280 module in place. The receiver has its own GPS so when it receives the location data from the tracker can calculate the distance and direction to the tracker.

![](/images/word-image-9.jpeg)

When the tracker was last received on 2.4Ghz LoRa® it was one its way down at a distance of 89.237km and an altitude of 6895km. From the field tracking location on the North Devon coast the balloon was tracked till just short of landing using 434Mhz LoRa® and FSK RTTY.

![](/images/word-image-10-1024x768.jpeg)

The balloon, call sign Vincent, landed after crossing the Bristol Channel from North Devon to land near Newcastle Emlyn, a distance of around 108km.

The 2.4Ghz LoRa® settings used were bandwidth 406khz and spreading factor 12. The lower bandwidth of 203khz should provide a bit more range with a bit of fine tuning.

One point to note is that the L70 GPS used, bought at very low cost from China, stopped working and lost lock when the balloon went above 10km. It was seen to recover when the balloon was on the way down at 6895m. The GPS was put into balloon mode so further tests are required to identify the cause of the problem.

The flight was an interesting test of the limits of 2.4Ghz LoRa® and whilst its not going to replace the standard UHF LoRa® devices for HAB tracking, as the in air range is only just adequate, reception distance at ground level especially with trees around is limited to a couple of hundred metres or so.

The ranging or distance measuring is an interesting feature and the device is capable of some fairly high data rates, 203kbps for LoRa® and the FLRC mode should cover the same distance but at a data rate of 975kbs.

It was fun to experiment, thanks to all those involved for the workshop with a special thanks to Bill Harvey who organised it.

**Stuart Robinson**
