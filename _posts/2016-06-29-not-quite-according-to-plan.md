---
layout: post
title: "Not quite according to plan"
date: "2016-06-29"
---

Conditions looked good for a test of my easy build Pico balloon tracker. Picos are typically 36” foil party balloons filled with just the right amount of helium so that they rise to around 8000M and continue to float along at that altitude. 

![CUSF Flight Prediction](/images/CUSF-Flight-Prediction_thumb.jpg "CUSF Flight Prediction")

The CUSF landing predictor suggested the balloon would from a Cardiff launch travel South, turn back North, circle back on itself, head North again and come back South over the Iceland and the West coast of the UK. It was not going to be quick but the battery would last around 6 days.
 

The tracker build for this flight was about as simple as it can get, just an Arduino Pro Mini, DRF1278F LoRa® transceiver, UBLOX Max8Q GPS, PCBs, batteries, pin headers and some wire, no other additional components needed. The tracker board is multi-purpose, designed to be used as lost model locator, balloon tracker, remote sensor or a portable receiver. Extra components can be added for the audio uplink into habitat, powering from primary and backup supplies, components to switch on\\off the GPS power and extra decoupling capacitors, if needed. There are connections available on the expansion header for an external serial or I2C GPS, a serial LCD display or a I2C environment sensor such as the BME280 or BMP280, you can even fit an independant watchdog supervisor.

![Tracker Front](/images/Tracker-Front_thumb.jpg "Tracker Front")

![Tracker Rear](/images/Tracker-Rear_thumb.jpg "Tracker Rear")

Note the essential inductor on the rear of the GPS PCB, this protects the antenna input from static damage. The angled pin headers can be cut off before flight. Once programmed the configuration of the tracker for other flights. different names, frequencies or LoRa® parameters etc. can be done remotely.

I went to a location just to the West of the Second Severn crossing with my pre inflated balloon on Saturday. I had with me my portable Arduino Pro Mini based LoRa® receiver and had left my bench receiver running at home. The bench receiver is a simple RFM98 shield plugged into a 3.3V ATMEGA2560 base.  This receiver, like the portable one, has an audio upload capability into HAB FLDIGI on a Windows laptop. The ‘sound’ card on the laptop was one of those £1 USB adapters, it quite reliably copes with RTTY upload at 1200baud.

![Bench Receiver](/images/Bench-Receiver_thumb.jpg "Bench Receiver")

![Portable Receiver](/images/Portable-Receiver_thumb.jpg "Portable Receiver")

I realise the plastic food containers used for the portable receiver look a little crude, but they are cheap, robust and easy to work.

The launch site on the foreshore was clear of any trees, and after I let it go the balloon rose steadily and soon drifted out of sight. 

![IMG_0250_Reduced](/images/IMG_0250_Reduced_thumb.jpg "IMG_0250_Reduced")

First LoRa® telemetry was received at home (31kM away) when the balloon had risen to 479M with the LoRa® signal received at -7dB SNR. The balloon continued to rise till it reached 1223M.

Then the balloon started to descend with the portable LoRa® receiver continuing to pick up signals until the balloon was just 60M above ground and 15kM away.  The balloon landed just beyond a small rise, the likely reason that reception stopped.

 
![Profile](/images/Profile_thumb.jpg "Profile")

**Launch position on left, final position extreme right.**

So whilst the flight definitely did not go as planned, at least I had an opportunity to try the search and find capability of LoRa® for real.

The portable LoRa® receiver uses the last recorded telemetry to calculate the distance and direction, and whilst this is stored in RAM, this information would be lost if the receiver was switched off or perhaps dropped. Small FRAM memories are inexpensive and the specified number of writes is several magnitudes greater than EEPROM  (1,000,000,000,000 writes) so you can treat them as RAM.

The benefit of the FRAM is that when you turn on the receiver it will use the stored last recorded telemetry as the trackers location, thus I knew I could use it to get very close to the landing position, especially as the tracker was only 60M above ground when the last position was recorded. 

![Lost Balloon](/images/Lost-Balloon_thumb.jpg "Lost Balloon")

I drove to Filton the next day (Sunday)  and picked up new LoRa® signals when my ‘chase’ car was 2.5kM away from the tracker. I drove in the direction indicated on the display and got to within 100M of the tracker. After a short walk around the edge of a wood, my receiver indicated the tracker was only 10M away and sure enough there it was ensconced on top of a tree.

I had originally thought the envelope had failed, causing the balloon to descend, but when found it looked to be normally inflated.

The receiver also links the LoRa® telemetry via Bluetooth to offline mapping OS mapping on an Android tablet, but that makes life just too easy…..

The tracker had been running for over 24 hours, the payload indicated it had consumed around 9mA average per hour, half that due to the FSK RTTY.

The 2 x AAA Lithium batteries would therefore be expected to last approximately  6 days running LoRa® and FSK RTTY transmissions and 12 days in LoRa® only mode. This was for a minimum component count tracker build using the software backup mode of the UBLOX GPS. This mode needs no extra components to switch off (most of the) power to the GPS which reduces to circa 500uA.

Power control components can be added to the tracker PCB to allow the GPS power consumption to be reduced to zero until a fix is needed. Total tracker sleep current then drops to 30uA. Depending on how often you then need to update the GPS fix battery life based on LoRa® only transmissions could be extended into several months.

The power savings in the main come form a much improved antenna for the GPS. Its common to fit a small ceramic stick antenna for the GPS on Pico balloons. This tracker was fitted with a ¾ wave wire antenna and ¼ wave radials (for 1575Mhz) and this gave about 8-10dB of gain over the typical ceramic stick. With the extra signal gain the GPS will operate very well in Hot Fix mode such that 95% of the time it can be powered off.

**And Finally ......**

What I should have done when I left the tracker in the tree on the Sunday is used my LoRa® receiver and remotely configure the tracker to turn off the FSK RTTY and extend the GPS fix time to 10 minutes or longer.  Without these changes the battery will be flat within 6 days. If the changes had been made the battery would have lasted a month or more. Thus if the tracker became dislodged from the tree I would have had a far better chance of finding it the following weekend.
